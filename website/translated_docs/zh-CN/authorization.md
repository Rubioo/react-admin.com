---
id: authorization
title: 授权
---
某些应用程序可能需要确定特定身份验证的用户对安全资源的访问级别。 由于有许多不同的可能策略 (单个角色、多个角色或权限等), 因此, react-admin 只需提供 hooks 即可执行您自己的授权代码。

默认情况下, react-admin 应用程序不需要授权。 但是, 如果需要, 它将依赖于 [Authentication](./Authentication.html) 部分中引入的 `authProvider`。

## 配置 Auth Provider

每次组件需要检查用户的权限时，都会调用具有 `AUTH_GET_PERMISSIONS` 类型的 `authProvider`。

下面是 `authProvider` 在身份验证时存储用户角色的示例，并在调用权限检查时返回它：

```jsx
// in src/authProvider.js
import { AUTH_LOGIN, AUTH_LOGOUT, AUTH_ERROR, AUTH_GET_PERMISSIONS } from 'react-admin';
import decodeJwt from 'jwt-decode';

export default (type, params) => {
    if (type === AUTH_LOGIN) {
        const { username, password } = params;
        const request = new Request('https://mydomain.com/authenticate', {
            method: 'POST',
            body: JSON.stringify({ username, password }),
            headers: new Headers({ 'Content-Type': 'application/json' }),
        })
        return fetch(request)
            .then(response => {
                if (response.status < 200 || response.status >= 300) {
                    throw new Error(response.statusText);
                }
                return response.json();
            })
            .then(({ token }) => {
                const decodedToken = decodeJwt(token);
                localStorage.setItem('token', token);
                localStorage.setItem('role', decodedToken.role);
            });
    }
    if (type === AUTH_LOGOUT) {
        localStorage.removeItem('token');
        localStorage.removeItem('role');
        return Promise.resolve();
    }
    if (type === AUTH_ERROR) {
        // ...
    }
    if (type === AUTH_CHECK) {
        return localStorage.getItem('token') ? Promise.resolve() : Promise.reject();
    }
    if (type === AUTH_GET_PERMISSIONS) {
        const role = localStorage.getItem('role');
        return role ? Promise.resolve(role) : Promise.reject();
    }
    return Promise.reject('Unkown method');
};
```

## 限制对资源或视图的访问

可以限制对 `Admin` 组件内的资源或其视图的访问。 为此，您必须将一个函数指定为仅 `Admin` 子项。 将使用 authProvider 返回的 permissions 调用此函数。

```jsx
<Admin
    dataProvider={dataProvider}
    authProvider={authProvider}
>
    {permissions => [
        // Restrict access to the edit and remove views to admin only
        <Resource
            name="customers"
            list={VisitorList}
            edit={permissions === 'admin' ? VisitorEdit : null}
            icon={VisitorIcon}
        />,
        // Only include the categories resource for admin users
        permissions === 'admin'
            ? <Resource name="categories" list={CategoryList} edit={CategoryEdit} icon={CategoryIcon} />
            : null,
    ]}
</Admin>
```

请注意，该函数返回一个 React 元素数组。 这是必须的，以避免必须将它们包装在容器元素中，这将阻止 `Admin` 工作。

提示即使可能，在完全排除资源时也要小心（比如本例中的 `categories` 资源），因为它也会阻止您在其他资源视图中引用它们。

## 限制对 Fields 和 Inputs 的访问

您可能希望仅向具有特定权限的用户显示某些字段或输入。 将为每个路径检索这些权限，并将其作为 `permissions` 属性提供给您的组件。

每个路由将使用 `AUTH_GET_PERMISSIONS` 类型和一些参数（包括当前位置和路由参数）调用 authProvider。 您可以在组件内部返回需要检查的内容，例如用户的角色等。

以下是使用 `SimpleForm` 和自定义 `Toolbar` 的 `Create` 视图中的示例：

```jsx
const UserCreateToolbar = ({ permissions, ...props }) =>
    <Toolbar {...props}>
        <SaveButton
            label="user.action.save_and_show"
            redirect="show"
            submitOnEnter={true}
        />
        {permissions === 'admin' &&
            <SaveButton
                label="user.action.save_and_add"
                redirect={false}
                submitOnEnter={false}
                variant="flat"
            />}
    </Toolbar>;

export const UserCreate = ({ permissions, ...props }) =>
    <Create {...props}>
        <SimpleForm
            toolbar={<UserCreateToolbar permissions={permissions} />}
            defaultValue={{ role: 'user' }}
        >
            <TextInput source="name" validate={[required()]} />
            {permissions === 'admin' &&
                <TextInput source="role" validate={[required()]} />}
        </SimpleForm>
    </Create>;
```

**提示</ strong>：请注意` permissions ` 属性如何传递给自定义` toolbar `组件。</p> 

这也适用于带` TabbedForm `的` Edition `视图，你可以完全隐藏` FormTab `：

```jsx
export const UserEdit = ({ permissions, ...props }) =>
    <Edit title={<UserTitle />} {...props}>
        <TabbedForm defaultValue={{ role: 'user' }}>
            <FormTab label="user.form.summary">
                {permissions === 'admin' && <DisabledInput source="id" />}
                <TextInput source="name" validate={required()} />
            </FormTab>
            {permissions === 'admin' &&
                <FormTab label="user.form.security">
                    <TextInput source="role" validate={required()} />
                </FormTab>}
        </TabbedForm>
    </Edit>;
```

List视图，DataGrid，SimpleList和Filter组件怎么样？ 它也适用于那里。

```jsx
const UserFilter = ({ permissions, ...props }) =>
    <Filter {...props}>
        <TextInput
            label="user.list.search"
            source="q"
            alwaysOn
        />
        <TextInput source="name" />
        {permissions === 'admin' ? <TextInput source="role" /> : null}
    </Filter>;

export const UserList = ({ permissions, ...props }) =>
    <List
        {...props}
        filters={<UserFilter permissions={permissions} />}
        sort={{ field: 'name', order: 'ASC' }}
    >
        <Responsive
            small={
                <SimpleList
                    primaryText={record => record.name}
                    secondaryText={record =>
                        permissions === 'admin' ? record.role : null}
                />
            }
            medium={
                <Datagrid>
                    <TextField source="id" />
                    <TextField source="name" />
                    {permissions === 'admin' && <TextField source="role" />}
                    {permissions === 'admin' && <EditButton />}
                    <ShowButton />
                </Datagrid>
            }
        />
    </List>;
```

**提示** 注意 `permissions` 属性如何传递到自定义 `filters` 组件。

## 限制对 Dashboard 内部内容的访问

作为 [`dashboard`]('./Admin.md#dashboard) 提供的组件也将在其属性中获得 permissions：

```jsx
// in src/Dashboard.js
import React from 'react';
import Card from '@material-ui/core/Card';
import CardContent from '@material-ui/core/CardContent';
import { ViewTitle } from 'react-admin';

export default ({ permissions }) => (
    <Card>
        <ViewTitle title="Dashboard" />
        <CardContent>Lorem ipsum sic dolor amet...</CardContent>
        {permissions === 'admin'
            ? <CardContent>Sensitive data</CardContent>
            : null
        }
    </Card>
);
```

## 限制对自定义页中内容的访问

您可能希望检查 [自定义页](./Admin.md#customroutes) 内的用户权限。 您必须为此使用 `WithPermissions` 组件。 它将确保用户通过身份验证，然后使用 `AUTH_GET_PERMISSIONS` type和您指定的 `authParams` 调用 `authProvider`：

```jsx
// in src/MyPage.js
import React from 'react';
import Card from '@material-ui/core/Card';
import CardContent from '@material-ui/core/CardContent';
import { ViewTitle, WithPermissions } from 'react-admin';
import { withRouter } from 'react-router-dom';

const MyPage = ({ permissions }) => (
    <Card>
        <ViewTitle title="My custom page" />
        <CardContent>Lorem ipsum sic dolor amet...</CardContent>
        {permissions === 'admin'
            ? <CardContent>Sensitive data</CardContent>
            : null
        }
    </Card>
)
const MyPageWithPermissions = ({ location, match }) => (
    <WithPermissions
        authParams={{ key: match.path, params: route.params }}
        // location is not required but it will trigger a new permissions check if specified when it changes
        location={location}
        render={({ permissions }) => <MyPage permissions={permissions} /> }
    />
);

export default MyPageWithPermissions;

// in src/customRoutes.js
import React from 'react';
import { Route } from 'react-router-dom';
import Foo from './Foo';
import Bar from './Bar';
import Baz from './Baz';
import MyPageWithPermissions from './MyPage';

export default [
    <Route exact path="/foo" component={Foo} />,
    <Route exact path="/bar" component={Bar} />,
    <Route exact path="/baz" component={Baz} noLayout />,
    <Route exact path="/baz" component={MyPageWithPermissions} />,
];
```

## 限制对自定义菜单中内容的访问

如果要检查自定义菜单中的权限，该怎么办？ 就像在自定义页面中获取权限一样，您必须使用 `WithPermissions` 组件：

```jsx
// in src/myMenu.js
import React from 'react';
import { connect } from 'react-redux';
import { MenuItemLink, WithPermissions } from 'react-admin';

const Menu = ({ onMenuClick, logout, permissions }) => (
    <div>
        <MenuItemLink to="/posts" primaryText="Posts" onClick={onMenuClick} />
        <MenuItemLink to="/comments" primaryText="Comments" onClick={onMenuClick} />
        <WithPermissions
            render={({ permissions }) => (
                permissions === 'admin'
                    ? <MenuItemLink to="/custom-route" primaryText="Miscellaneous" onClick={onMenuClick} />
                    : null
            )}
        />
        {logout}
    </div>
);
```