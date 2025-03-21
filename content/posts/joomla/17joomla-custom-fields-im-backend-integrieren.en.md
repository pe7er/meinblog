---
date: 2020-12-17
title: 'Joomla 4.x Tutorial - Extension Development - Integrate Custom Fields in Backend'
template: post
thumbnail: '../../thumbnails/joomla.png'
slug: en/joomla-custom-fields-im-backend-integrieren
langKey: en
categories:
  - JoomlaEn
  - Code
tags:
  - CMS
  - Joomla
---

Custom Fields (Own Fields)<!-- \index{custom fields!backend} --> are very popular since Joomla version 3.7. They offer many additional possibilities. Therefore, there is no question that we integrate them into our component.

This part shows you how to program the support in the administration area. In the next chapter we will integrate Custom Fields in the frontend.

> For impatient people: View the changed program code in the [Diff View](https://github.com/astridx/boilerplate/compare/t13...t14a)[^github.com/astridx/boilerplate/compare/t13...t14a] and copy these changes into your development version.

## Step by step

### New files

We have not created a new file in this part, we have only changed files.

### Modified files

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/access.xml](https://github.com/astridx/boilerplate/compare/t13...t14a#diff-9e0953a0d7bd9275f21f699e0722ed100264bd28b3b9452cabcde1e393603e93)

In the file `administrator/components/com_foos/ access.xml` we prepare everything to give permissions to the user-defined fields. So it is possible that only specific users are allowed to change or view a field.

[administrator/components/com_foos/access.xml](https://github.com/astridx/boilerplate/blob/t14a/src/administrator/components/com_foos/access.xml)

```xml {diff}
 		<action name="core.edit.state" title="JACTION_EDITSTATE" />
 		<action name="core.edit.own" title="JACTION_EDITOWN" />
 	</section>
+	<section name="fieldgroup">
+		<action name="core.create" title="JACTION_CREATE" />
+		<action name="core.delete" title="JACTION_DELETE" />
+		<action name="core.edit" title="JACTION_EDIT" />
+		<action name="core.edit.state" title="JACTION_EDITSTATE" />
+		<action name="core.edit.own" title="JACTION_EDITOWN" />
+		<action name="core.edit.value" title="JACTION_EDITVALUE" />
+	</section>
+	<section name="field">
+		<action name="core.delete" title="JACTION_DELETE" />
+		<action name="core.edit" title="JACTION_EDIT" />
+		<action name="core.edit.state" title="JACTION_EDITSTATE" />
+		<action name="core.edit.value" title="JACTION_EDITVALUE" />
+	</section>
 </access>
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/config.xml](https://github.com/astridx/boilerplate/compare/t13...t14a#diff-9be56d6cedb2c832265e47642f0afb25)

The configuration `config.xml` uses a paramter to define whether the extension uses custom fields.

> Do you wonder why this parameter exists? It is [not mandatory](https://joomla.stackexchange.com/questions/28672/reason-for-parameter-for-using-custom-fields-in-configuration)[^https://joomla.stackexchange.com/questions/28672/reason-for-parameter-for-using-custom-fields-in-configuration].

[administrator/components/com_foos/config.xml](https://github.com/astridx/boilerplate/blob/t14a/src/administrator/components/com_foos/config.xml)

```php {diff}
 			<option value="0">JNO</option>
 			<option value="1">JYES</option>
 		</field>
+
+		<field
+			name="custom_fields_enable"
+			type="radio"
+			label="JGLOBAL_CUSTOM_FIELDS_ENABLE_LABEL"
+			layout="joomla.form.field.radio.switcher"
+			default="1"
+			>
+			<option value="0">JNO</option>
+			<option value="1">JYES</option>
+		</field>
 	</fieldset>
 	<fieldset
 		name="permissions"
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ foos.xml](https://github.com/astridx/boilerplate/compare/t13...t14a#diff-2fc2de3e713c03872261cc037f7f6194d843f9cbc953d34db10e9693dfa82924)

In the navigation menu on the left in the Joomla administration area we add two links. The first new link leads to the view where custom fields are created for the component. The other one leads to the view where field groups are created.

[administrator/components/com_foos/ foos.xml](https://github.com/astridx/boilerplate/blob/t14a/src/administrator/components/com_foos/foos.xml)

```php {diff}
 		<submenu>
 			<menu link="option=com_foos">COM_FOOS</menu>
 			<menu link="option=com_categories&amp;extension=com_foos">JCATEGORY</menu>
+			<menu link="option=com_fields&amp;context=com_foos.foo">JGLOBAL_FIELDS</menu>
+			<menu link="option=com_fields&amp;view=groups&amp;context=com_foos.foo">JGLOBAL_FIELD_GROUPS</menu>
 		</submenu>
 		<files folder="administrator/components/com_foos">
 			<filename>access.xml</filename>

```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ src/Model/FooModel.php](https://github.com/astridx/boilerplate/compare/t13...t14a#diff-b50434577837f29c2b0bf385fb9e14819e0ab8fb1557b6d88583896698e7a7c0)

The form through which a Foo element can be edited now has tabs. To ensure that the data is not lost within the session when switching between tabs, we change the `loadFormData()` method in the file `administrator/components/com_foos/ src/Model/FooModel.php`. It is not necessary that we cache data ourselves. The method `$app->getUserState()` does this for us. At the same time we make sure that a default value is set for the category if a new element is loaded and therefore `$this->getState('foo.id') == 0` equals `true`.

[administrator/components/com_foos/ src/Model/FooModel.php](https://github.com/astridx/boilerplate/blob/t14a/src/administrator/components/com_foos/src/Model/FooModel.php)

```php {diff}

 	{
 		$app = Factory::getApplication();

-		$data = $this->getItem();
+		// Check the session for previously entered form data.
+		$data = $app->getUserState('com_foos.edit.foo.data', []);
+
+		if (empty($data)) {
+			$data = $this->getItem();
+
+			// Prime some default values.
+			if ($this->getState('foo.id') == 0) {
+				$data->set('catid', $app->input->get('catid', $app->getUserState('com_foos.foos.filter.category_id'), 'int'));
+			}
+		}

 		$this->preprocessData($this->typeAlias, $data);

```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ tmpl/foo/edit.php](https://github.com/astridx/boilerplate/compare/t13...t14a#diff-1637778e5f7d1d56dd1751af1970f01b)

To make editing the custom fields work the same way as in Joomla's own extensions, we use [UiTab](https://github.com/joomla/joomla-cms/blob/4.0-dev/libraries/src/HTML/Helpers/UiTab.php)[^github.com/joomla/joomla-cms/blob/4.0-dev/ libraries/src/html/helpers/uitab.php]. `$this->useCoreUI = true;` ensures that the [Helper](https://github.com/joomla/joomla-cms/blob/4.0-dev/layouts/joomla/edit/params.php#L20)[^github.com/joomla/joomla-cms/blob/4.0-dev/ layouts/joomla/edit/params.php#l20] flexibly provides the correct tab implementation.

> A comparison between previously most used `bootstrap.tab` and `uitab` is provided by [Pull Request PR 21805](https://github.com/joomla/joomla-cms/pull/21805)[^github.com/joomla/joomla-cms/pull/21805].<!-- \index{bootstrap.tab} --><!-- \index{uitab} -->

[administrator/components/com_foos/ tmpl/foo/edit.php](https://github.com/astridx/boilerplate/blob/02dd34246bf4a070fcc7b2d7b1dfe5015d0d6c54/src/administrator/components/com_foos/tmpl/foo/edit.php)

```php {diff}
 use Joomla\CMS\Factory;
 use Joomla\CMS\HTML\HTMLHelper;
 use Joomla\CMS\Router\Route;
+use Joomla\CMS\Language\Text;
+use Joomla\CMS\Layout\LayoutHelper;

 $app = Factory::getApplication();
 $input = $app->input;

+$this->useCoreUI = true;
+
 $wa = $this->document->getWebAssetManager();
 $wa->useScript('keepalive')
 	->useScript('form.validate')
 ?>

 <form action="<?php echo Route::_('index.php?option=com_foos&layout=' . $layout . $tmpl . '&id=' . (int) $this->item->id); ?>" method="post" name="adminForm" id="foo-form" class="form-validate">
-	<?php echo $this->getForm()->renderField('name'); ?>
-	<?php echo $this->getForm()->renderField('alias'); ?>
-	<?php echo $this->getForm()->renderField('access'); ?>
-	<?php echo $this->getForm()->renderField('catid'); ?>
-	<?php echo $this->getForm()->renderField('published'); ?>
-	<?php echo $this->getForm()->renderField('publish_up'); ?>
-	<?php echo $this->getForm()->renderField('publish_down'); ?>
+	<div>
+		<?php echo HTMLHelper::_('uitab.startTabSet', 'myTab', ['active' => 'details']); ?>
+
+		<?php echo HTMLHelper::_('uitab.addTab', 'myTab', 'details', empty($this->item->id) ? Text::_('COM_FOOS_NEW_FOO') : Text::_('COM_FOOS_EDIT_FOO')); ?>
+		<div class="row">
+			<div class="col-md-9">
+				<div class="row">
+					<div class="col-md-6">
+						<?php echo $this->getForm()->renderField('name'); ?>
+						<?php echo $this->getForm()->renderField('alias'); ?>
+						<?php echo $this->getForm()->renderField('access'); ?>
+						<?php echo $this->getForm()->renderField('published'); ?>
+						<?php echo $this->getForm()->renderField('publish_up'); ?>
+						<?php echo $this->getForm()->renderField('publish_down'); ?>
+						<?php echo $this->getForm()->renderField('catid'); ?>
+					</div>
+				</div>
+			</div>
+		</div>
+		<?php echo HTMLHelper::_('uitab.endTab'); ?>
+
+		<?php echo LayoutHelper::render('joomla.edit.params', $this); ?>
+
+		<?php echo HTMLHelper::_('uitab.endTabSet'); ?>
+	</div>
 	<input type="hidden" name="task" value="">
 	<?php echo HTMLHelper::_('form.token'); ?>
 </form>

```

## Test your Joomla component

1. install your component in Joomla version 4 to test it: Copy the files in the `administrator` folder into the `administrator` folder of your Joomla 4 installation. A new installation is not necessary. Continue using the files from the previous part.

2. Open the view of your component in the administration area. You will see entries in the navigation on the left. Click on the menu item 'Fields' in this new menu.

![Integrate Joomla Custom Fields into a custom component](/images/j4x17x10.png)

3. after that create a custom field of type 'text'.

4. make sure that when you edit a foo item, this custom field is also offered for editing.

![Integrate Joomla Custom Fields into a custom component](/images/j4x17x2.png)

5. make sure that the custom fields can be turned on and off in the global configuration.

![Integrate Joomla Custom Fields into a custom component](/images/j4x17x3.png)
<img src="https://vg08.met.vgwort.de/na/b7801b02af92421e8755fa873ca33ca5" width="1" height="1" alt="">
