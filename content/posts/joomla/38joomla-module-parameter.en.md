---
date: 2021-01-07
title: 'Joomla 4.x Tutorial - Extension Development - Modules - Namespace and Helper'
template: post
thumbnail: '../../thumbnails/joomla.png'
slug: en/joomla-module-parameter
langKey: en
categories:
  - JoomlaEn
  - Code
tags:
  - CMS
  - Joomla
---

Via [Parameter](<https://en.wikipedia.org/wiki/Parameter_(computer_programming)>), the Joomla module can be flexibly adapted for end users. Parameters are variables through which Joomla is set to process certain values. In other words, parameters are influencing factors set externally to the programme. They are used to tell the module externally which data should be processed and how.<!-- \index{module!parameter} -->

> For impatient people: Look at the changed programme code in the [Diff View](https://github.com/astridx/boilerplate/compare/t32...t33)[^github.com/astridx/boilerplate/compare/t32...t33] and copy these changes into your development version.

## Step by step

### New files

In this section we add parameters to the module.

### New files

In this part, only files have been changed. There are no new files.

### Changed files

#### Module

<!-- prettier-ignore -->
##### [modules/mod\_foo/ language/en-GB/en-GB.mod_foo.ini](https://github.com/astridx/boilerplate/compare/t32...t33#diff-9c4225bbdf2ea51af1036568f0f1e8817ecc47e86d001366d2278a2e7281281a)

The labelling of the parameter in the backend should adapt to the active language. For this reason we use the language file.

> Are you wondering about the prefix `COM_` in `COM_MODULES_FOOPARAMS_FIELDSET_LABEL`? The language string is automatically created by Joomla because a fieldset called `fooparams` is added.

[modules/mod_foo/ language/en-GB/en-GB.mod_foo.ini](https://github.com/astridx/boilerplate/blob/b8c783812c9acf66a6c0c0a534d5d43b987510c5/src/modules/mod_foo/language/en-GB/en-GB.mod_foo.ini)

```php {diff}
MOD_FOO="[PROJECT_NAME]"
MOD_FOO_XML_DESCRIPTION="Foo Module"
+MOD_FOO_FIELD_URL_LABEL="URL"
+COM_MODULES_FOOPARAMS_FIELDSET_LABEL="Foo Parameter"

```

<!-- prettier-ignore -->
##### [modules/mod\_foo/ mod_foo.php](https://github.com/astridx/boilerplate/compare/t32...t33#diff-43348bdc6a37cd697897d234acd68a56c191ded22f30b54aa8de2e9c099b9c84)

In the module's initial file `modules/mod_foo/ mod_foo.php`, we check which value the parameter is set to and store it in a variable. This way, uncomplicated access is possible later.

[modules/mod_foo/ mod_foo.php](https://github.com/astridx/boilerplate/blob/183694b03393699bf3af10f5dd0207188a97cb31/src/modules/mod_foo/mod_foo.php)

```php {diff}
$test  = FooHelper::getText();

+$url = $params->get('domain');
+
 require ModuleHelper::getLayoutPath('mod_foo', $params->get('layout', 'default'));
```

<!-- prettier-ignore -->
##### [modules/mod\_foo/ mod_foo.xml](https://github.com/astridx/boilerplate/compare/t32...t33#diff-c111dcc16cb14017dbacf97ab7d495ac6e7225b2b2097774adc23a977d5cc3c3)

In the manifest we add the new parameter so that it is editable in the Joomla backend.

[modules/mod_foo/ mod_foo.xml](https://github.com/astridx/boilerplate/blob/b8c783812c9acf66a6c0c0a534d5d43b987510c5/src/modules/mod_foo/mod_foo.xml)

```php {diff}
 		<folder>language</folder>
 		<filename>mod_foo.xml</filename>
 	</files>
+	<config>
+		<fields name="params">
+			<fieldset name="fooparams">
+				<field
+					name="domain"
+					type="url"
+					label="MOD_FOO_FIELD_URL_LABEL"
+					filter="url"
+				/>
+			</fieldset>
+		</fields>
+	</config>
 </extension>

```

> Use `<fieldset name="basic">` to display the parameters in the first tab that opens immediately.

In addition to the parameters that a developer inserts into his module, there are standard parameters that Joomla handles itself. For example `/administrator/components/com_modules/forms/advanced.xml`.

![Joomla Module Parameters](/images/j4x38x1.png)

<!-- prettier-ignore -->
##### [modules/mod\_foo/tmpl/default.php](https://github.com/astridx/boilerplate/compare/t32...t33#diff-5dc488d0a39079a73583a37bf1b465fcf99ca183970958084a2eac52f723a4ba)

In the module's own template file, we can now access the value of the parameter. In the following example, we output the value as text. Usually a parameter is used in a more complex way, for example within control structures such as if-statements or loops.

> An example of the more complex use of a parameter is a digital map where parameters are used to enable controls such as `locate me` or a choice of map type.

[modules/mod_foo/tmpl/default.php](https://github.com/astridx/boilerplate/blob/b8c783812c9acf66a6c0c0a534d5d43b987510c5/src/modules/mod_foo/tmpl/default.php)

```php {diff}
 \defined('_JEXEC') or die;

-echo '[PROJECT_NAME]' . $test;
+echo '[PROJECT_NAME]' . $test . '<br />' . $url;

```

## Test your Joomla module

1. install the module in Joomla version 4 to test it:

Copy the files in the `modules` folder into the `modules` folder of your Joomla 4 installation.

A new installation is not necessary. Continue using the files from the previous part.

2. Check the presence of the parameter in the backend.

![Test Joomla Module](/images/j4x38x3.png)

3. make sure that the value of the parameter is taken into account in the frontend display.

![Test Joomla Module](/images/j4x38x2.png)

## Links

[Joomla Dokumentation](https://docs.joomla.org/J4.x:Creating_a_Simple_Module)[^https://docs.joomla.org/j4.x:creating_a_simple_module]
<img src="https://vg08.met.vgwort.de/na/af6ae71bda914ca3ae8f2a484bc39f18" width="1" height="1" alt="">
