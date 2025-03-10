---
date: 2021-01-10
title: 'Joomla 4.x-Tutorial - Extension Development - Template - Module Positions'
template: post
thumbnail: '../../thumbnails/joomla.png'
slug: en/joomla-template-positionen
langKey: en
categories:
  - JoomladE
  - Code
tags:
  - CMS
  - Joomla
---

Our template should dynamically display the Joomla content from components, modules and plugins at different positions. How module positions are integrated in the template is the topic of this chapter.<!-- \index{template!positions} -->

> For impatient people: Take a look at the changed programme code in the [Diff-Ansicht](https://github.com/astridx/boilerplate/compare/t35...t36)[^github.com/astridx/boilerplate/compare/t35...t36] and copy these changes into your development version.

## Step by step

We will proceed step by step. In this part we add the module positions so that Joomla displays content dynamically. We will take care of the design in the next part.

### New files

The only changes made in this section are.

### Changed files

#### Template

So far, we have a static website. In this part, we add content dynamically using module positions.

##### [templates/facile/component.php](https://github.com/astridx/boilerplate/blob/161043cf57d9bd06df1d23803db2cd1ed7aacbca/src/templates/facile/component.php)

I already mentioned it in the previous part: The file `component.php` displays only main content. This is the content of type `component`. Navigation and content in sidebars are omitted. For testing we had created this file. So far it contained only the static text `Component`. Now we extend it by its actual task. A minimal structure looks like this.

[templates/facile/component.php](https://github.com/astridx/boilerplate/blob/161043cf57d9bd06df1d23803db2cd1ed7aacbca/src/templates/facile/component.php)

```php {diff}
-Component
+<!DOCTYPE html>
+<html lang="de">
+<head>
+	<meta name="viewport" content="width=device-width, initial-scale=1">
+	<jdoc:include type="head" />
+</head>
+<body>
+	<jdoc:include type="message" />
+	<jdoc:include type="component" />
+</body>
+</html>
+
```

The main new entry is `<jdoc:include type="component" />`. The command inserts the main content of the current page.

In addition, we use `<jdoc:include type="message" />` and `<jdoc:include type="head" />`. `<jdoc:include type="message" />` displays system and error messages that occurred during the request. `<jdoc:include type="head" />` loads content that requires extensions and includes them via special commands. These are mainly scripts and styles.

> `<jdoc:include type="head" />`, `<jdoc:include type="message" />` and `<jdoc:include type="component" />` should appear only once in the `<body>` element of the template. More information about the commands can be found in the Joomla documentation [docs.joomla.org/Jdoc_statements/en](https://docs.joomla.org/Jdoc_statements/de).

##### [templates/facile/index.php](https://github.com/astridx/boilerplate/compare/t34...t35#diff-6155acc1859344bb0cdb1ef792d0107971f0d60c87f3fc3138e9672a2b924931)

You already know it: The file `index.php` is the heart of the template. It makes sure that everything works together. In the previous chapter we had not integrated Joomla's own content. I will make up for this here. A minimal structure, which inserts the Joomla content, looks like this.

[templates/facile/index.php](https://github.com/astridx/boilerplate/blob/159271f625aac7d0ce5e7fdffd033e6c28097647/src/templates/facile/index.php)

```php {diff}
 	<meta name="viewport" content="width=device-width, initial-scale=1.0">
 	<title>Titel</title>
   </head>
+
   <body>
-	Hallo Joomla!
-  </body>
+  <header>
+	<div>
+	  <nav>
+	      <div>
+		     <jdoc:include type="modules" name="topbar" />
+	      </div>
+
+	      <div>
+		     <jdoc:include type="modules" name="below-topbar" />
+	      </div>
+
+				<div>
+					<jdoc:include type="modules" name="menu" />
+				</div>
+			</nav>
+			<div>
+				<jdoc:include type="modules" name="search" />
+			</div>
+		</div>
+	</header>
+
+	<div>
+		<jdoc:include type="modules" name="banner" />
+	</div>
+
+	<div>
+		<jdoc:include type="modules" name="top-a" />
+	</div>
+
+	<div>
+		<jdoc:include type="modules" name="top-b" />
+	</div>
+
+	<div>
+		<jdoc:include type="modules" name="sidebar-left" />
+	</div>
+
+	<div>
+		<jdoc:include type="modules" name="breadcrumbs" />
+		<jdoc:include type="modules" name="main-top" />
+		<jdoc:include type="message" />
+		<main>
+		<jdoc:include type="component" />
+		</main>
+		<jdoc:include type="modules" name="main-bottom" />
+	</div>
+
+	<div>
+		<jdoc:include type="modules" name="sidebar-right" />
+	</div>
+
+	<div>
+		<jdoc:include type="modules" name="bottom-a" />
+	</div>
+
+	<div>
+		<jdoc:include type="modules" name="bottom-b" />
+	</div>
+
+	<footer>
+		<jdoc:include type="modules" name="footer" />
+	</footer>
+
+  <jdoc:include type="modules" name="debug" />
+
+</body>
 </html>
```

> Inside the header area Joomla templates load header information with `<jdoc: include type="head" />` via Joomla API. We already use this above in the `component.php` file. The `jdoc:include` command inserts the necessary header information. This way you are on the safe side. I don`t use this command here, because I want to choose what I need in the main view itself.

We can find the `jdoc:include` command in other places in `index.php`. For example, we see `<jdoc:include type="message" />`, so the system messages work. Whenever Joomla has something to tell the website visitor, this line will display it on the screen. For example, when sending an email through a contact form, you will see the message "Your message was sent successfully".

Another element to discuss is `<jdoc:include type="component" />`. This element inserts the main content into the site.

The last element worth mentioning is `<jdoc:include type="modules" />`. As the name suggests, this is used to include modules.

So, enough explained. All contents are included. They are not displayed nicely yet. Don't be scared if you open this version in your browser later. You will see all content in unstyled form at the moment.

##### [templates/facile/language/en-GB/en-GB.tpl_facile.sys.ini](https://github.com/astridx/boilerplate/compare/t35...t36#diff-764a4776e5fab4421733468c2fc87d67e612f3d84297fb83ed0495d4c04b27d2)

Via the language files it is possible to describe the positions exactly. Note the line `TPL_FACILE_POSITION_TOP-A="Area under banner"`. `TOP-A` does not mean much to a user. He understands `Area under banner`.

![Create Joomla Template - Name Module Positions](/images/j4x41x1.png)

[templates/facile/language/en-GB/en-GB.tpl_facile.sys.ini](https://github.com/astridx/boilerplate/compare/t35...t36#diff-764a4776e5fab4421733468c2fc87d67e612f3d84297fb83ed0495d4c04b27d2)

```php {diff}

 FACILE="Facile - Site template"
+TPL_FACILE_POSITION_MENU="Menu"
+TPL_FACILE_POSITION_SEARCH="Search"
+TPL_FACILE_POSITION_BANNER="Banner"
+TPL_FACILE_POSITION_TOP-A="Area under banner"
+TPL_FACILE_POSITION_TOP-B="Area above the content"
+TPL_FACILE_POSITION_MAIN-TOP="Main-top"
+TPL_FACILE_POSITION_BREADCRUMBS="Breadcrumbs"
+TPL_FACILE_POSITION_MAIN-BOTTOM="Main-bottom"
+TPL_FACILE_POSITION_SIDEBAR-LEFT="Sidebar-left"
+TPL_FACILE_POSITION_SIDEBAR-RIGHT="Sidebar-right"
+TPL_FACILE_POSITION_BOTTOM-A="Bottom-a"
+TPL_FACILE_POSITION_BOTTOM-B="Bottom-b"
+TPL_FACILE_POSITION_FOOTER="Footer"
+TPL_FACILE_POSITION_DEBUG="Debug"
+TPL_FACILE_POSITION_TOPBAR="Top Bar"
+TPL_FACILE_POSITION_BELOW-TOP="Below Top"
```

##### [templates/facile/templateDetails.xml](https://github.com/astridx/boilerplate/compare/t35...t36#diff-7d97de6b92def4b5a42a0052c815e6fada268a2e2dda9e3ea805eb87e0076dc1)

In the file `templateDetails.xml` the module positions are inserted to be selectable as position when creating a module. So a module is includeable via the command `jdoc:include` in the `index.php`.

[src/templates/facile/templateDetails.xml](https://github.com/astridx/boilerplate/blob/8238130b429378f62cf6652b2f77255a62a7380d/src/templates/facile/templateDetails.xml)

```xml {diff}

 		<filename>template_thumbnail.png</filename>
 		<folder>language</folder>
 	</files>
+
+	<positions>
+		<position>topbar</position>
+		<position>below-top</position>
+		<position>menu</position>
+		<position>search</position>
+		<position>banner</position>
+		<position>top-a</position>
+		<position>top-b</position>
+		<position>main-top</position>
+		<position>main-bottom</position>
+		<position>breadcrumbs</position>
+		<position>sidebar-left</position>
+		<position>sidebar-right</position>
+		<position>bottom-a</position>
+		<position>bottom-b</position>
+		<position>footer</position>
+		<position>debug</position>
+	</positions>
 </extension>

```

### Changed files

Only files have been added in this section.

## Test your Joomla template

1. install your template in Joomla version 4 to test it:

Copy the files in the `templates` folder to the `templates` folder of your Joomla 4 installation.

A new installation is not necessary. Continue using the ones from the previous part.

3. install the sample data, so that you have the same prerequisites as I have.

![Create Joomla Template - Install sample files](/images/j4x41x2.png)

4. test now, if the sample files are displayed correctly. Activate the template style Cassiopei and call the URL `joomla-cms4/index.php`. How you change a template style, I had shown in the previous chapter with a picture. Your view should be like in the following picture.

![Create Joomla Template - View in Cassiopeia](/images/j4x41x4.png)

5. next test if our template _Facile_ works without errors. Activate the Template Style Facile and call the URL `joomla-cms4/index.php` again. Your view should be like in the following picture.

![Create Joomla Template - View Facil ungestyled](/images/j4x41x3.png)

You can view the module positions in the frontend. Activate the view in the global configuration in the backend and call the URL `joomla-cms4/index.php?tp=1`. The appendage `?tp=1` is crucial.

![Create Joomla Template - Show Module Positions - Backend](/images/j4x41x5.png)

![Create Joomla Template - Show Module Positions - Frontend](/images/j4x41x6.png)

This does not look inviting. I agree with you there. So next we pep up the template with CSS and JavaScipt and adjust the default views of Joomla.
<img src="https://vg08.met.vgwort.de/na/7174fafb8ead4309b9cfe82778799426" width="1" height="1" alt="">
