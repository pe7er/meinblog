---
date: 2021-01-04
title: 'Joomla 4.x Tutorial - Extension Development - Plugins'
template: post
thumbnail: '../../thumbnails/joomla.png'
slug: en/joomla-plugins
langKey: en
categories:
  - JoomlaEn
  - Code
tags:
  - CMS
  - Joomla
---

You have created a plug-in in the previous section. You have probably already configured other plugins in the plugin manager and know the different types. Plugins cover many different areas in Joomla. This chapter provides an overview of what plugins are and how they work within Joomla.<!-- \index{plugins} -->

> In the [Joomla Documentation](https://docs.joomla.org/Plugin/Events)[^docs.joomla.org/plugin/events] you will find a list of all plugin groups with all associated events. Use this list as a quick reference when you have a task to do.

## What is a Joomla plugin?

You already know that there are different types of extensions: Components, modules, templates, languages and plugins. While components, modules, templates and languages usually cause a direct output, a plugin typically works in the background. Plugins are versatile. Each plugin has its own purpose.
Let's organise plugins a little. Even within Joomla, they are divided into plugin groups. It's much easier to understand the purpose if you look at each type separately. For example, content plugins and system plugins. Fortunately, it is not difficult to write a sample plugin. You already saw that in the last chapter. In this chapter, we will get an overview of the different types and their special features.

## Plugin types in the Joomla 4 core

The Joomla core comes with a lot of plugins. These are divided into 22 plugin types and so is this part of the text. For example, there is a chapter about content plugins and another one about system plugins.

> For an overview of all plugins available in Joomla core and their associated events/events, see the [Joomla documentation](https://docs.joomla.org/Help4.x:Plugins:_Name_of_Plugin)[^docs.joomla.org/help4.x:plugins:_name_of_plugin]. Check out the code if you need some programming inspiration.

In my opinion, it helps to understand Joomla plugins if you study each type on its own. That is why we are doing this now. The types or groups are classified as follows:

### [Action Log](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Action_Log_Group/en)<!-- \index{plugins!Action Log} -->

Plugins of the Action Log type record user activities in the Joomla Core extensions of the page to review them later if needed. If you want to log activities in a third-party extension, create a plugin of this type for it.

### [API Authentication](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_API_Authentication_Group/en)<!-- \index{plugins!API Authentication} -->

API Authentication type plugins are used to provide authentication for web services in Joomla. Remember: You activated a Joomla core plugin of this type in the previous chapter on web services.

### [Authentication](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Authentication_Group/en)<!-- \index{plugins!Authentication} -->

When someone logs into Joomla, the Joomla application authenticates that user. On most websites, authentication is performed against the Joomla database. This type of authentication is performed by the authentication plugin. With an authentication plugin, it is possible to use external services to authenticate users: Joomla provides an authentication plugin for LDAP, which is used in Windows domains.

> Joomla 3 had plugins for authentication via Gmail on board. [Joomla 4 no longer offers this](https://developer.joomla.org/news/724-removal-of-the-gmail-authentication-plugin-as-of-joomla-4-0.html)[^developer.joomla.org/news/724-removal-of-the-gmail-authentication-plugin-as-of-joomla-4-0.html]. The technology used by the plugin is no longer state of the art and less secure. Nowadays, applications should authorize themselves via the [OAuth 2.0](https://oauth.net/)[^oauth.net] protocol with Google.

### [Behaviour](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Behaviour_Group/en)<!-- \index{plugins!Behaviour} -->

Behavior type plugins are used to enable a specific behavior in the website. Examples in Joomla core are tagging or versioning of elements.

### [CAPTCHA](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_CAPTCHA_Group/en)<!-- \index{plugins!CAPTCHA} -->

Plugins of this group allow to check forms with a [Captcha Check](https://en.wikipedia.org/wiki/Captcha)[^en.wikipedia.org/wiki/captcha] (engl. completely automated public Turing test to tell computers and humans apart), a fully automated public Turing test that detects whether a human or a machine submits the form. The Joomla core comes with a plugin for [Google reCaptcha](https://www.google.com/recaptcha/about/)[^google.com/recaptcha/about/]. Custom captcha methods are easily added.

> Captchas are a nice way to add an individual touch to the website. If it is too much work to create images that match the topic, you can work with questions. On the website of a fire department association a possible question would be, about the color of the fire truck.

### [Content](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Content_Group/en)<!-- \index{plugins!Content} -->

A content plugin is mostly used to change the content of the article before it is displayed or before it is saved in the database. Those who have special requirements can use a plugin of this type for custom functions after the article is saved in the database. Whenever you want to customize the processing of the content, choosing this type of plugin is perfect.

### [Editors](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Editor_Group/en)<!-- \index{plugins!Editors} -->

Editor plugins convert an HTML textarea element into a JavaScript-based editor. Well known plugins of this group are TinyMCE and CodeMirror. If no [WYSIWYG](https://en.wikipedia.org/wiki/WYSIWYG)[^en.wikipedia.org/wiki/wysiwyg] editor plugin is enabled, Joomla displays a normal HTML textarea. Technically, this is also done via a plugin, namely via `Editor | None`.

> A third party plugin from the Editor group, which is very popular in the Joomla community, is the [JCE-Editor](https://www.joomlacontenteditor.net/)[^https://www.joomlacontenteditor.net/].

### [Editor Button](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Button_Group/en)<!-- \index{plugins!Editor Button} -->

At the bottom of a Joomla editor, buttons appear in addition to the toolbar - for example, a button to add a `read more` link or a button to add a page break. These buttons are generated by plugins of the editors-xtd type.

### [Extensions](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Extension_Group/en)<!-- \index{plugins!Extensions} -->

There are not many plugins in this group, nevertheless it is an interesting group. Whenever a Joomla extension is installed or removed, it is possible to hook into the installation via a plugin of this group.An extension plugin does a task during an installation! The Joomla plugin of extension type is used to clean up update pages. Update pages are URLs that are stored in the extension manager for updating extensions. Since Joomla 3.2 it is possible for commercial extensions to use this plugin to allow private downloads with a security key. `Extensions - Namespace Updater` automatically creates and updates the file `administrator/cache/autoload_psr4.php`.

### [Fields](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Fields_Group/en)<!-- \index{plugins!Fields} -->

The Fields plugin type allows you to create fields in extensions that support custom fields. For example, a calendar can be added when creating an item, through which a date is stored with the item, which is output at a specific location in the content. This makes it easier to output content in the same layout or to query content in other extensions. For example, a field that stores a geographic coordinate will display a marker at that position on a digital map in a module.

### [FileSystem](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_FileSystem_Group/en)<!-- \index{plugins!FileSystem} -->

Plugins of the Filesystem type are used to define one or more local directories for storing files. Do you want to offer the flexible changing of a directory for your extension. Then check out the Joomla core plugin `Filesystem - Local` with which you can set the directory where image files are stored.

### [Finder](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Smart_Search_Group/en)<!-- \index{plugins!Finder} -->

The default search in Joomla 4 is the Search Index or Smart Search component: com_finder. In Joomla 3 this was com_search. The main difference between the two is that com_search searches the content in real time and may open many different database tables to do so, while com_finder creates index tables first and then searches only that index. The latter allows for a more efficient and therefore faster full-text search. The new search index is more complex than the old classic search, which required no configuration but offered few options. com_finder uses an active index based on [stem reduction](https://en.wikipedia.org/wiki/Stemming)[^en.wikipedia.org/wiki/stemming]. Specifically, the [PHP library php-stemmer](https://github.com/wamania/php-stemmer)[github.com/wamania/php-stemmer] is applied. The idea is to increase the performance and quality of the search result by covering multiple syntactic words with a base form. For example, `gardening` and `garden` have related meanings. Each type of content requires its own Finder plugin. Create a Finder plugin if you want content in your component to be found,

> com_search is [still available](https://github.com/joomla-extensions/search)[^github.com/joomla-extensions/search] as a decoupled component, and it also requires a separate plugin for third-party extension content to be found.

### [Installer](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Installer_Group/en)<!-- \index{plugins!Installer} -->

Do you want to change the installation process of your extension? Then take a look at the installer type plugins.

### [Media Action](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Media_Action_Group/en)<!-- \index{plugins!Media Action} -->

Cropping images, changing the size or rotating them is each possible with a core Joomla plugin from the Media Action. Expand this plugin group if the media or image editing functions are not enough for you.

### [Privacy](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Privacy_Group/en)<!-- \index{plugins!Privacy} -->

If your self-programmed extension processes personal data, then plugins of the type Privacy come into play. Create a plugin of this type and make sure in the code that this data is correctly processed by Joomla in the core privacy component. This is the only way Joomla can handle user requests for stored data or deletion requests. For Joomla core extensions the required plugins are available in Joomla 4.

### [Quick Icon](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Quick_Icon_Group/en)<!-- \index{plugins!Quick Icon} -->

Use a plugin of the type quickicon to place a quickicon on the dashboard of the Joomla backend.

### [Sample Data](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Sample_Data_Group/en)<!-- \index{plugins!Sample Data} -->

The _Joomla Core Sample Data module_ provides a unified workflow for adding sample files. Want to jump in here and make sample files installable for your extension with a click? Then, you probably guessed it already, a plugin of the type sample files is required.

### [System](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_System_Group/en)<!-- \index{plugins!System} -->

System Plugins performs a wide variety of tasks. This sounds vague, however. To make it a bit more concrete, examples follow. System plugins can add HTML code, CSS or JavaScript to the Joomla page after it is generated.Plugins of this type modify Joomla forms before they are generated. With the help of system plugins alternative error handling is possible. This was only a small part of the possible. You see, system plugins are very powerful. To be able to fulfill this powerful task, they are called frequently and therefore need resources. Use them carefully!

### [Two Factor Authentication](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Two_Factor_Authentication_Group/en)<!-- \index{plugins!Two Factor Authentication} -->

In addition to standard authentication, there is the possibility to achieve additional security by adding a parallel second authentication.

### [User](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_User_Group/en)<!-- \index{plugins!User} -->

Is there a connection between the data in a component and the users in the Joomla user management? Technically, this is implemented with a plug-in of the user type. Are you wondering how this works? Then take a look at the plugin for the contact component, which links a contact to a user.

### [Web Services](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Web_Services_Group/en)<!-- \index{plugins!Web Services} -->

A Web Services plug-in adds the routes of an extension to the website's API. We practically used this plugin in the previous part.

### [Workflow](https://docs.joomla.org/Chunk4x:Extensions_Plugin_Manager_Edit_Workflow_Group/en)<!-- \index{plugins!Workflow} -->

In workflow management, there are different transitions that can be manipulated using a plugin.
<img src="https://vg08.met.vgwort.de/na/6ed60508849f482daea03626b68de768" width="1" height="1" alt="">
