---
id: translation
title: 翻译
---
React-admin 界面使用英语作为默认语言。但是它也支持任何其他语言，归功于[polyglot.js]（http://airbnb.io/polyglot.js/）库。

## 改变区域设置

如果要使用其他语言环境，则必须安装第三方软件包。 例如，要将界面更改为法语，您必须安装`ra-language-french` npm包，然后指示 react-admin 使用它。

`<Admin>` 组件有一个 `i18nProvider` 属性，它接受一个具有以下签名的函数：

```js
const i18nProvider = locale => messages;
```

` messages `应该是界面和资源名称的字典（有关字典格式的详细信息，请参阅下面的[翻译消息部分](#translation-messages)）。

React-admin 在启动时调用 `i18nProvider`，将 Admin 组件上指定的语言 `local` 作为参数传递。 Provide 必须同步返回消息。 每当语言环境发生变化时，React-admin 也会调用 i18nProvider，并将新语言环境作为参数传递。 因此，多语言界面的最简单示例如下：

```jsx
import React from 'react';
import { Admin, Resource } from 'react-admin';
import frenchMessages from 'ra-language-french';
import englishMessages from 'ra-language-english';

const messages = {
    fr: frenchMessages,
    en: englishMessages,
}
const i18nProvider = locale => messages[locale];

const App = () => (
    <Admin locale="en" i18nProvider={i18nProvider}>
        ...
    </Admin>
);

export default App;
```

` i18nProvider `可能会返回一个区域设置更改调用的Promise（初始调用除外，应用程序启动时）。 这对于仅加载所需的区域设置非常有用。 例如：

```js
import englishMessages from '../en.js';

const asyncMessages = {
    fr: () => import('../i18n/fr.js').then(messages => messages.default),
    it: () => import('../i18n/it.js').then(messages => messages.default),
};

const i18nProvider = locale => {
    if (locale === 'en') {
        // initial call, must return synchronously
        return englishMessages;
    }
    // change of locale after initial call returns a promise
    return asyncMessages[params.locale]();
}

const App = () => (
    <Admin locale="en" i18nProvider={i18nProvider}>
        ...
    </Admin>
);
```

## 可用区域设置

您可以找到以下语言的翻译包：

- Chinese (`cn`): [chen4w/ra-language-chinese](https://github.com/chen4w/ra-language-chinese)
- Czech (`cs`): [binao/ra-language-czech](https://github.com/binao/ra-language-czech)
- Dutch (`nl`): [pimschaaf/ra-language-dutch](https://github.com/pimschaaf/ra-language-dutch)
- English (`en`): [marmelab/ra-language-english](https://github.com/marmelab/react-admin/tree/master/packages/ra-language-english)
- French (`fr`): [marmelab/ra-language-french](https://github.com/marmelab/react-admin/tree/master/packages/ra-language-french)
- German (`de`): [greenbananaCH/ra-language-german](https://github.com/greenbananaCH/ra-language-german)
- Indonesian (`id`): [ronadi/ra-language-indonesian](https://github.com/ronadi/ra-language-indonesian)
- Italian (`it`): [stefsava/ra-language-italian](https://github.com/stefsava/ra-language-italian)
- Polish (`pl`): [tskorupka/ra-language-polish](https://github.com/tskorupka/ra-language-polish)
- Portuguese (`pt`): [marquesgabriel/ra-language-portuguese](https://github.com/marquesgabriel/ra-language-portuguese)
- Russian (`ru`): [klucherev/ra-language-russian](https://github.com/klucherev/ra-language-russian)
- Slovak (`sk`): [zavadpe/ra-language-slovak](https://github.com/zavadpe/ra-language-slovak)
- Spanish (`es`): [blackboxvision/ra-language-spanish](https://github.com/BlackBoxVision/ra-language-spanish)
- Ukrainian (`ua`): [koresar/ra-language-ukrainian](https://github.com/koresar/ra-language-ukrainian)

之前版本的 react-admin，名为 admin-on-rest，已翻译成以下语言：

- Arabic ( `ع` ): [aymendhaya/aor-language-arabic](https://github.com/aymendhaya/aor-language-arabic)
- Chinese (`cn`): [downup2u/aor-language-chinese](https://github.com/downup2u/aor-language-chinese)
- Chinese (Traditional) (`cht`): [leesei/aor-language-chinese-traditional](https://github.com/leesei/aor-language-chinese-traditional)
- Croatian (`hr`): [ariskemper/aor-language-croatian](https://github.com/ariskemper/aor-language-croatian)
- Czech (`cs`): [magikMaker/aor-language-czech](https://github.com/magikMaker/aor-language-czech)
- Danish (`da`): [SSA111/aor-language-danish](https://github.com/SSA111/aor-language-danish)
- Farsi (`fa`): [hamidfzm/aor-language-farsi](https://github.com/hamidfzm/aor-language-farsi)
- Finnish (`fi`): [Joni-Aaltonen/aor-language-finnish](https://github.com/Joni-Aaltonen/aor-language-finnish)
- German (`de`): [der-On/aor-language-german](https://github.com/der-On/aor-language-german)
- Greek (`el`): [zifnab87/aor-language-greek](https://github.com/zifnab87/aor-language-greek)
- Hebrew (`he`): [motro/aor-language-hebrew](https://github.com/motro/aor-language-hebrew)
- Hungarian (`hu`): [s33m4nn/aor-language-hungarian](https://github.com/s33m4nn/aor-language-hungarian)
- Indonesian (`id`): [ronadi/aor-language-indonesian](https://github.com/ronadi/aor-language-indonesian)
- Japanese (`ja`): [kuma-guy/aor-language-japanese](https://github.com/kuma-guy/aor-language-japanese)
- Norwegian (`nb`): [zeusbaba/aor-language-norwegian](https://github.com/zeusbaba/aor-language-norwegian)
- Russian (`ru`): [cytomich/aor-language-russian](https://github.com/cytomich/aor-language-russian)
- Slovenian (`sl`): [ariskemper/aor-language-slovenian](https://github.com/ariskemper/aor-language-slovenian)
- Spanish (`es`): [blackboxvision/aor-language-spanish](https://github.com/BlackBoxVision/aor-language-spanish)
- Swedish (`sv`): [StefanWallin/aor-language-swedish](https://github.com/StefanWallin/aor-language-swedish)
- Thai (`th`): [liverbool/aor-language-thai](https://github.com/liverbool/aor-language-thai)
- Turkish (`tr`): [ismailbaskin/aor-language-turkish](https://github.com/ismailbaskin/aor-language-turkish)
- Ukrainian (`uk`): [vitivs/aor-language-ukrainian](https://github.com/vitivs/aor-language-ukrainian)
- Vietnamese (`vi`): [kimkha/aor-language-vietnamese](https://github.com/kimkha/aor-language-vietnamese)

这些软件包不能与 react-admin 直接互操作，但升级很简单; 将 root key 从 “aor” 重命名为 “ra”。 我们邀请上面列出的软件包的作者使用不同的软件包名称为 react-admin 重新发布他们的翻译。

如果您想提供新的翻译，请随时提交 pr 以更新[此页面](https://github.com/marmelab/react-admin/blob/master/docs/Translation.md)，并附带指向您软件包的链接。

## 在运行时更改区域设置

如果要在运行时提供更改语言环境的功能，则必须提供所有可能翻译的 messages：

```jsx
import React from 'react';
import { Admin, Resource } from 'react-admin';
import englishMessages from 'ra-language-english';
import frenchMessages from 'ra-language-french';

const messages = {
    fr: frenchMessages,
    en: englishMessages,
};
const i18nProvider = locale => messages[locale];

const App = () => (
    <Admin locale="en" i18nProvider={i18nProvider}>
        ...
    </Admin>
);

export default App;
```

然后，使用 `changeLocale</ code> 动作创建者 dispatch <code>CHANGE_LOCALE` action。 例如，以下组件在英语和法语之间切换语言：

```jsx
import React, { Component } from 'react';
import { connect } from 'react-redux';
import Button from '@material-ui/core/Button';
import { changeLocale as changeLocaleAction } from 'react-admin';

class LocaleSwitcher extends Component {
    switchToFrench = () => this.props.changeLocale('fr');
    switchToEnglish = () => this.props.changeLocale('en');

    render() {
        const { changeLocale } = this.props;
        return (
            <div>
                <div>Language</div>
                <Button onClick={this.switchToEnglish}>en</Button>
                <Button onClick={this.switchToFrench}>fr</Button>
            </div>
        );
    }
}

export default connect(undefined, { changeLocale: changeLocaleAction })(LocaleSwitcher);
```

## 使用浏览器区域设置

React-admin 提供一个名为 `resolveBrowserLocale()` 的帮助器函数，它帮助您根据用户浏览器中配置的区域设置来引入动态区域设置属性。 要使用它，只需将函数传递作为 `locale` 属性。

```jsx
import React from 'react';
import { Admin, Resource, resolveBrowserLocale } from 'react-admin';
import englishMessages from 'ra-language-english';
import frenchMessages from 'ra-language-french';

const messages = {
    fr: frenchMessages,
    en: englishMessages,
};
const i18nProvider = locale => messages[locale];

const App = () => (
    <Admin locale={resolveBrowserLocale()} i18nProvider={i18nProvider}>
        ...
    </Admin>
);

export default App;
```

## 翻译 Messages

`i18nProvider` 值返回的 `message` 应该是一个字典，其中键标界面组件，值是翻译后的字符串。 这个字典是一个简单的 JavaScript 对象，如下所示：

```jsx
{
    ra: {
        action: {
            delete: 'Delete',
            show: 'Show',
            list: 'List',
            save: 'Save',
            create: 'Create',
            edit: 'Edit',
            cancel: 'Cancel',
        },
        ...
    },
}
```

所有核心翻译都在 `ra` 命名空间中，以防止与您自己的自定义翻译冲突。 运行时使用的 root key 由 `locale` 属性的值决定。

可用的默认 [messages ](https://github.com/marmelab/react-admin/blob/master/packages/ra-language-english/index.js)

## 翻译 Resource 和 Field 名称

默认情况下，React-admin在界面中的任何位置使用 resource 名称（“post”，“comment”等）和 field 名称（“title”，“first_name”等）。 它简单地“人性化”技术标识符，使其看起来更好（例如“first_name”变为“First name”）。

但是，在人性化名称之前，react-admin 会使用以下键检查 `messages` 字典以查找可能的翻译：

- `${locale}.resources.${resourceName}.name` 用于资源名称（用于菜单和页面标题）
- `${locale}.resources.${resourceName}.fields.${fieldName}`用于字段名称（用于datagrid header和表单输入标签）

这使您可以通过使用 `resources` 键传递 `messages` 对象来翻译自己的 resource 和 field 名称：

```jsx
{
    resources: {
        shoe: {
            name: 'Shoe |||| Shoes',
            fields: {
                model: 'Model',
                stock: 'Nb in stock',
                color: 'Color',
            },
        },
        customer: {
            name: 'Customer |||| Customers',
            fields: {
                first_name: 'First name',
                last_name: 'Last name',
                dob: 'Date of birth',
            }
        }
    },
    ...
}
```

如您所见, [polyglot pluralization](http://airbnb.io/polyglot.js/#pluralization) 在这里使用，但它是可选的。

使用 `resources` 键是在 Field 和 Input 组件中使用 `label` 属性的替代方法，具有支持翻译的优势。

## 混合界面和域翻译

当翻译admin时，界面消息（例如“List”，“Page”等）通常来自第三方软件包，而您的域信息（例如“Shoe”，“Date of birth”等）来自你自己的代码。 这意味着在将这些消息传递到 `<Admin>` 之前, 您需要合并这些 messages 。 组合消息的方法是使用ES6解构：

```jsx
// interface translations
import englishMessages from 'ra-language-english';
import frenchMessages from 'ra-language-french';

// domain translations
import * as domainMessages from './i18n';

const messages = {
    fr: { ...frenchMessages, ...domainMessages.fr },
    en: { ...englishMessages, ...domainMessages.en },
};
const i18nProvider = locale => messages[locale];

const App = () => (
    <Admin i18nProvider={i18nProvider}>
        ...
    </Admin>
);
```

## 翻译自己的组件

翻译系统使用React `context` 在组件树中传递翻译。 要翻译一个句子，请使用上下文中的 `translate` 函数。 当然，这假设你以前已经将相应的翻译添加到 `Admin` 组件的`messages` 属性中。

```jsx
// in src/MyHelloButton.js
import React, { Component } from 'react';
import PropTypes from 'prop-types';

class MyHelloButton {
    render() {
        const { translate } = this.context;
        return <button>{translate('myroot.hello.world')}</button>;
    }
}
MyHelloButton.contextTypes = {
    translate: PropTypes.func,
};

// in src/App.js
const messages = {
    en: {
        myroot: {
            hello: {
                world: 'Hello, World!',
            },
        },
    },
};
```

然而，使用上下文使组件更难测试。 这就是为什么 react-admin 提供了一个 `translate` 高阶组件，它只是将`translate` 函数从上下文传递给属性：

```jsx
// in src/MyHelloButton.js
import React from 'react';
import { translate } from 'react-admin';

const MyHelloButton = ({ translate }) => (
    <button>{translate('myroot.hello.world')}</button>
);

export default translate(MyHelloButton);
```

**提示</ strong>：对于您的 message 标识符，请选择与 ` ra` 和 ` resources` 不同的根名称，这些名称是保留的。</p> 

**提示</ strong>：请勿对字段和输入标签或页面标题使用` translate `，因为它们已被翻译：</p> 

```jsx
// don't do this
<TextField source="first_name" label={translate('myroot.first_name')} />

// do this instead
<TextField source="first_name" label="myroot.first_name" />

// or even better, use the default translation key
<TextField source="first_name" />
// and translate the `resources.customers.fields.first_name` key
```

## 使用特定Polyglot Features

Polyglot.js是一个很棒的库：除了小，完全维护和完全框架不可知外，它还提供了一些很好的功能，例如插值和复数，可以在 react-admin 中使用。

```jsx
const messages = {
    'hello_name': 'Hello, %{name}',
    'count_beer': 'One beer |||| %{smart_count} beers',
}

// interpolation
translate('hello_name', { name: 'John Doe' });
=> 'Hello, John Doe.'

// pluralization
translate('count_beer', { smart_count: 1 });
=> 'One beer'

translate('count_beer', { smart_count: 2 });
=> '2 beers'

// default value
translate('not_yet_translated', { _: 'Default translation' })
=> 'Default translation'
```

要查找更详细的示例，请参阅[ http://airbnb.io/polyglot.js/ ](http://airbnb.io/polyglot.js/)

## Notifications With Variables

可以使用自定义通知传递多语言插值的变量。 例如：

```js
showNotification('myroot.hello.world', 'info', { messageArgs: { name: 'Planet Earth' } });
```

假设您的自定义消息中包含以下内容：

```js
// in src/App.js
const messages = {
    en: {
        myroot: {
            hello: {
                world: 'Hello, %{name}!',
            },
        },
    },
};
```