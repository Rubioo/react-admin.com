---
id: show-view-component
title: Show 视图组件
---
Show 视图以只读方式显示从 API 获取的记录。 它将记录的实际呈现方式委托给一个布局组件——通常是 `<SimpleShowLayout>`。 此布局组件使用其子级 ([`<Fields>`](./Fields.md) 组件) 来呈现每个记录字段。

![post show view](https://marmelab.com/react-admin/img/show-view.png)

## `Show` 组件

`<Show>` 组件呈现页标题和 action, 并从 REST API 中提取记录。 它不负责呈现实际记录-这是其子组件 (通常为 `<SimpleShowLayout>`) 的工作, 它们通过 `record` 作为属性。

以下是 `<Show>` 组件所接受的所有属性：

* [`title`](#page-title)
* [`actions`](#actions)

以下是 show 视图显示帖子所需的最少代码：

```jsx
// in src/App.js
import React from 'react';
import { Admin, Resource } from 'react-admin';
import jsonServerProvider from 'ra-data-json-server';

import { PostCreate, PostEdit, PostShow } from './posts';

const App = () => (
    <Admin dataProvider={jsonServerProvider('http://jsonplaceholder.typicode.com')}>
        <Resource name="posts" show={PostShow} create={PostCreate} edit={PostEdit} />
    </Admin>
);

export default App;

// in src/posts.js
import React from 'react';
import { Show, SimpleShowLayout, TextField, DateField, EditButton, RichTextField } from 'react-admin';

export const PostShow = (props) => (
    <Show {...props}>
        <SimpleShowLayout>
            <TextField source="title" />
            <TextField source="teaser" />
            <RichTextField source="body" />
            <DateField label="Publication date" source="created_at" />
        </SimpleShowLayout>
    </Show>
);
```

That's enough to display the post show view:

![post show view](https://marmelab.com/react-admin/img/post-show.png)

### Page Title

默认情况下，Show 视图的显示标题是"[resource_name] #[record_id]"。

您可以通过指定一个自定义 `title` 属性来自定义此标题：

```jsx
export const PostShow = (props) => (
    <Show title="Post view" {...props}>
        ...
    </Show>
);
```

更有趣的是，你可以通过传递一个组件作为 `title`。 React-admin 克隆此组件，并在 `<ShowView>` 中注入当前 `record`。 这允许根据当前记录自定义标题：

```jsx
const PostTitle = ({ record }) => {
    return <span>Post {record ? `"${record.title}"` : ''}</span>;
};
export const PostShow = (props) => (
    <Show title={<PostTitle />} {...props}>
        ...
    </Show>
);
```

### Actions

你可以通过你自己的元素使用 `actions` 属性来替换默认 action 列表：

```jsx
import CardActions from '@material-ui/core/CardActions';
import Button from '@material-ui/core/Button';
import { ListButton, EditButton, DeleteButton, RefreshButton } from 'react-admin';

const cardActionStyle = {
    zIndex: 2,
    display: 'inline-block',
    float: 'right',
};

const PostShowActions = ({ basePath, data, resource }) => (
    <CardActions style={cardActionStyle}>
        <EditButton basePath={basePath} record={data} />
        <ListButton basePath={basePath} />
        <DeleteButton basePath={basePath} record={data} resource={resource} />
        <RefreshButton />
        {/* Add your custom actions */}
        <Button color="primary" onClick={customAction}>Custom Action</Button>
    </CardActions>
);

export const PostShow = (props) => (
    <Show actions={<PostShowActions />} {...props}>
        ...
    </Show>
);
```

## `SimpleShowLayout` 组件

`<SimpleShowLayout>`组件从它的父组件接收 `record` 作为属性。它负责渲染实际的视图。

`<SimpleShowLayout>` 逐行呈现其子组件（在`<div>`组件内）。

```jsx
export const PostShow = (props) => (
    <Show {...props}>
        <SimpleShowLayout>
            <TextField source="title" />
            <RichTextField source="body" />
            <NumberField source="nb_views" />
        </SimpleShowLayout>
    </Show>
);
```

可以通过指定 `style` 属性来重写其样式，例如：

```jsx
const styles = {
    container: {
        display: 'flex',
    },
    item: {
        marginRight: '1rem',
    },
};

export const PostShow = (props) => (
    <Show {...props}>
        <SimpleShowLayout style={styles.container}>
            <TextField source="title" style={styles.item} />
            <RichTextField source="body" style={styles.item} />
            <NumberField source="nb_views" style={styles.item} />
        </SimpleShowLayout>
    </Show>
);
```

## `TabbedShowLayout` 组件

就像 `<SimpleShowLayout>`, `<TabbedShowLayout>` 接收到 `record` 属性并渲染实际视图。 但是, `<TabbedShowLayout>` 组件按 tab 分组渲染。 这些选项卡是使用 `<Tab>` 组件设置的, 该元件期望一个 `label` 和可选的 `icon` 属性。 切换 tab 将更新当前 url。 默认情况下，它使用选项卡索引，第一个选项卡将显示在根URL。 您可以通过为每个Tab组件提供` path ` 属性来自定义路径。 如果您希望第一个充当索引页面，只需省略` path` 属性。

![tabbed show](https://marmelab.com/react-admin/img/tabbed-show.gif)

```jsx
import { TabbedShowLayout, Tab } from 'react-admin'

export const PostShow = (props) => (
    <Show {...props}>
        <TabbedShowLayout>
            <Tab label="summary">
                <TextField label="Id" source="id" />
                <TextField source="title" />
                <TextField source="teaser" />
            </Tab>
            <Tab label="body" path="body">
                <RichTextField source="body" addLabel={false} />
            </Tab>
            <Tab label="Miscellaneous" path="miscellaneous">
                <TextField label="Password (if protected post)" source="password" type="password" />
                <DateField label="Publication date" source="published_at" />
                <NumberField source="average_note" />
                <BooleanField label="Allow comments?" source="commentable" defaultValue />
                <TextField label="Nb views" source="views" />
            </Tab>
            <Tab label="comments" path="comments">
                <ReferenceManyField reference="comments" target="post_id" addLabel={false}>
                    <Datagrid>
                        <TextField source="body" />
                        <DateField source="created_at" />
                        <EditButton />
                    </Datagrid>
                </ReferenceManyField>
            </Tab>
        </TabbedShowLayout>
    </Show>
);
```

## 根据用户权限显示字段

您可能希望只向具有特定权限的用户显示某些字段。为每个路由检索这些权限, 并将其作为 `permissions` 属性提供给您的组件。

每个路由将使用 `AUTH_GET_PERMISSIONS` 类型和一些参数（包括当前位置和路由参数）调用 authProvider。 您可以在组件内部返回需要检查的内容，例如用户的角色等。

下面是 `Show` 视图中的一个示例，其中有一个 `SimpleShowLayout` 和一个自定义 `actions` 组件:

```jsx
import CardActions from '@material-ui/core/CardActions';
import Button from '@material-ui/core/Button';
import { ListButton, EditButton, DeleteButton } from 'react-admin';

const cardActionStyle = {
    zIndex: 2,
    display: 'inline-block',
    float: 'right',
};

const PostShowActions = ({ permissions, basePath, data, resource }) => (
    <CardActions style={cardActionStyle}>
        <EditButton basePath={basePath} record={data} />
        <ListButton basePath={basePath} />
        {permissions === 'admin' &&
            <DeleteButton basePath={basePath} record={data} resource={resource} />
        }
    </CardActions>
);

export const PostShow = ({ permissions, ...props }) => (
    <Show actions={<PostShowActions permissions={permissions} />} {...props}>
        <SimpleShowLayout>
            <TextField source="title" />
            <RichTextField source="body" />
            {permissions === 'admin' &&
                <NumberField source="nb_views" />
            }
        </SimpleShowLayout>
    </Show>
);
```

**提示** 请注意权限属性如何传递给自定义 `过滤器` 组件。

这也适用于TabbedShowLayout，您可以完全隐藏Tab：

```jsx
export const UserShow = ({ permissions, ...props }) =>
    <Show {...props}>
        <TabbedShowLayout>
            <Tab label="user.form.summary">
                {permissions === 'admin' && <TextField source="id" />}
                <TextField source="name" />
            </Tab>
            {permissions === 'admin' &&
                <Tab label="user.form.security">
                    <TextField source="role" />
                </Tab>}
        </TabbedShowLayout>
    </Show>;
```