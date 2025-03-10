---
date: 2020-12-12
title: 'Joomla 4.x-Tutorial - Entwicklung von Erweiterungen - Access Control List (ACL)'
template: post
thumbnail: '../../thumbnails/joomla.png'
slug: joomla-acl
langKey: de
categories:
  - JoomlaDe
  - Code
tags:
  - CMS
  - Joomla
---

Nicht jeder hat das Recht, alle Inhalte zu bearbeiten. Dazu bietet Joomla eine Zugriffskontrollliste, die ACL. Mit dieser handhabst du Benutzerrechte in deiner Komponente.<!-- \index{Zugriffskontrollliste} -->

> Für Ungeduldige: Sieh dir den geänderten Programmcode in der [Diff-Ansicht](https://github.com/astridx/boilerplate/compare/t9...t10)[^github.com/astridx/boilerplate/compare/t9...t10] an und übernimm diese Änderungen in deine Entwicklungsversion.

## Schritt für Schritt

### Neue Dateien

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ access.xml](https://github.com/astridx/boilerplate/compare/t9...t10#diff-e5dfd09c647ca1e552c9016cf918acf3)

Als erstes legen wir alle möglichen Berechtigungen in einer XML-Datei fest. Jede Komponente kann individuelle Berechtigungen definieren. Ich orientiere mich hier an den Aktionen, die in Joomla üblich sind. `core.admin` legt dabei fest, welche Gruppen die Berechtigungen auf Komponentenebene über die Schaltfläche `Optionen` in der Symbolleiste konfigurieren dürfen. `core.manage` bestimmt, welche Gruppen auf das Backend der Komponente zugreifen dürfen.

> Egal ob du individuellen Rechteanforderungen hast oder ob du dich ebenfalls an Joomla orientieren magst und unsicher bist, ist das [Tutorial zur Zugriffssteuerungsliste (ACL)](https://docs.joomla.org/J3.x:Access_Control_List_Tutorial/de)[^docs.joomla.org/j3.x:access_control_list_tutorial/de] eine hilfreiche Lektüre.

[administrator/components/com_foos/ access.xml](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/access.xml)

```xml {numberLines: -2}
<!-- https://raw.githubusercontent.com/astridx/boilerplate/t10/src/administrator/components/com_foos/access.xml -->

<?xml version="1.0" encoding="utf-8"?>
<access component="com_foos">
	<section name="component">
		<action name="core.admin" title="JACTION_ADMIN" />
		<action name="core.options" title="JACTION_OPTIONS" />
		<action name="core.manage" title="JACTION_MANAGE" />
		<action name="core.create" title="JACTION_CREATE" />
		<action name="core.delete" title="JACTION_DELETE" />
		<action name="core.edit" title="JACTION_EDIT" />
		<action name="core.edit.state" title="JACTION_EDITSTATE" />
		<action name="core.edit.own" title="JACTION_EDITOWN" />
	</section>
</access>
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ sql/updates/mysql/10.0.0.sql](https://github.com/astridx/boilerplate/compare/t9...t10#diff-887ce564d59a60e62da6554aa4e91cd7)

Die Berechtigungen speichert Joomla in der Datenbank. Bei einem Joomla-Update sind lediglich Datenbankänderungen relevant. Diese tragen wir in die Datei `administrator/components/com_foos/sql/updates/mysql/VERSIONSNUMMER.sql` ein, in unserem Fall ist dies konkret die Datei `administrator/components/com_foos/sql/updates/mysql/10.0.0.sql`. Diese Datei wird ausschließlich bei einer Aktualisierung aufgerufen. Bei einer neuen Installation wird die Datenbank über die Hauptdatei `administrator/components/com_foos/sql/install.mysql.utf8.sql` gleich korrekt eingerichtet.

[administrator/components/com_foos/ sql/updates/mysql/10.0.0.sql](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/sql/updates/mysql/10.0.0.sql)

```xml {numberLines: -2}
<!-- https://raw.githubusercontent.com/astridx/boilerplate/t10/src/administrator/components/com_foos/sql/updates/mysql/10.0.0.sql -->

ALTER TABLE `#__foos_details` ADD COLUMN  `access` int(10) unsigned NOT NULL DEFAULT 0 AFTER `alias`;

ALTER TABLE `#__foos_details` ADD KEY `idx_access` (`access`);
```

### Geänderte Dateien

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ config.xml](https://github.com/astridx/boilerplate/compare/t9...t10#diff-9be56d6cedb2c832265e47642f0afb25)

Die Berechtigungen für die gesamte Komponente stellen wir in der Konfiguration ein. Hierfür integrieren wir ein spezielles Formularfeld. Joomla bietet hierfür den Typ `rules`.

[administrator/components/com_foos/ config.xml](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/config.xml)

```xml {diff}
 			<option value="1">JYES</option>
 		</field>
 	</fieldset>
+	<fieldset
+		name="permissions"
+		label="JCONFIG_PERMISSIONS_LABEL"
+		description="JCONFIG_PERMISSIONS_DESC"
+		>
+		<field
+			name="rules"
+			type="rules"
+			label="JCONFIG_PERMISSIONS_LABEL"
+			validate="rules"
+			filter="rules"
+			component="com_foos"
+			section="component"
+		/>
+	</fieldset>
 </config>
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ foos.xml](https://github.com/astridx/boilerplate/compare/t9...t10#diff-1ff20be1dacde6c4c8e68e90161e0578)

Damit bei der Installation alles glatt läuft, ergänzen wir die hier neu hinzukommenden Dateien. Hierbei handelt es sich um `sql/updates/mysql` und `access.xml`.

[administrator/components/com_foos/ foos.xml](https://github.com/astridx/boilerplate/blob/fcce13afc14c13603509611630f369b2d53864c1/src/administrator/components/com_foos/foos.xml)

```xml {diff}
 			<file driver="mysql" charset="utf8">sql/uninstall.mysql.utf8.sql</file>
 		</sql>
 	</uninstall>
+	<update>  <!-- Runs on update -->
+		<schemas>
+			<schemapath type="mysql">sql/updates/mysql</schemapath>
+		</schemas>
+	</update>
 	<!-- Frond-end files -->
 	<files folder="components/com_foos">
 		<folder>language</folder>

 			<menu link="option=com_foos">COM_FOOS</menu>
 		</submenu>
 		<files folder="administrator/components/com_foos">
+			<filename>access.xml</filename>
 			<filename>foos.xml</filename>
 			<filename>config.xml</filename>
 			<folder>forms</folder>
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ forms/foo.xml](https://github.com/astridx/boilerplate/compare/t9...t10#diff-262e27353fbe755d3813ea2df19cd0ed)

Das Formular zum Erstellen eines neuen Foo-Items erweitern wir um die Möglichkeit, Berechtigungen für ein einzelnes Element zu setzten. Wir ergänzen das Feld `name="access"`.

[administrator/components/com_foos/ forms/foo.xml](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/forms/foo.xml)

```xml {diff}
 			size="45"
 			hint="JFIELD_ALIAS_PLACEHOLDER"
 		/>
+
+		<field
+			name="access"
+			type="accesslevel"
+			label="JFIELD_ACCESS_LABEL"
+			size="1"
+		/>
 	</fieldset>
 </form>
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ sql/install.mysql.utf8.sql](https://github.com/astridx/boilerplate/compare/t9...t10#diff-896f245bc8e493f91277fd33913ef974)

Das SQL-Skript für eine neue Installation der Komponente wird ebenfalls um die notwendigen Felder erweitert. So stellen wir sicher, dass die Datenbank bei einer neuen Installation ebenfalls vollständig eingerichtet wird.

[administrator/components/com_foos/ sql/install.mysql.utf8.sql](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/sql/install.mysql.utf8.sql)

```xml {diff}
 ('Nina'),
 ('Astrid'),
 ('Elmar');
+
+ALTER TABLE `#__foos_details` ADD COLUMN  `access` int(10) unsigned NOT NULL DEFAULT 0 AFTER `alias`;
+
+ALTER TABLE `#__foos_details` ADD KEY `idx_access` (`access`);
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ src/Model/FoosModel.php](https://github.com/astridx/boilerplate/compare/t9...t10#diff-2daf62ad6c51630353e31eaa3cc28626)

Wenn du mit SQL bisher nicht vertraut bist, dann wird die Datenbankabfrage im Model dir nun komplex erscheinen. Es ist jetzt erforderlich, Daten aus zwei Datenbanktabellen zu kombinieren. Die eine Tabelle ist `#__viewlevels`, welche die die Berechtigungen von `com_user` verwaltet. Die andere Tabelle ist die unserer Beispielkomponente welche `#__foos_details` benannt ist. Lass dich davon nicht abschrecken. Joomla unterstützt beim Erstellen der Abfragen.

[administrator/components/com_foos/ src/Model/FoosModel.php](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/src/Model/FoosModel.php)

```php {diff}

 		// Select the required fields from the table.
 		$query->select(
-			$db->quoteName(array('id', 'name', 'alias'))
+			$db->quoteName(array('a.id', 'a.name', 'a.alias', 'a.access'))
 		);
-		$query->from($db->quoteName('#__foos_details'));
+
+		$query->from($db->quoteName('#__foos_details', 'a'));
+
+		// Join over the asset groups.
+		$query->select($db->quoteName('ag.title', 'access_level'))
+			->join(
+				'LEFT',
+				$db->quoteName('#__viewlevels', 'ag') . ' ON ' . $db->quoteName('ag.id') . ' = ' . $db->quoteName('a.access')
+			);

 		return $query;
 	}
```

> Zur Erinnerung: Joomla unterstützt dich beim Erstellen der Datenbankabfragen. Wenn du die [zur Verfügung stehenden Anweisungen](https://docs.joomla.org/Accessing_the_database_using_JDatabase/de)[^docs.joomla.org/accessing_the_database_using_jdatabase/de] nutzt, dann kümmert sich Joomla um Sicherheit oder unterschiedliche Syntax in PostgreSQL und MySQL.

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ src/View/Foos/HtmlView.php](https://github.com/astridx/boilerplate/compare/t9...t10#diff-8e3d37bbd99544f976bf8fd323eb5250)

Eine Schaltfläche zum Erstellen eines Elementes ist nur sinnvoll, wenn dies erlaubt ist. Deshalb ändern wir die View ab - `$canDo` kommt hinzu. `$canDo = ContentHelper::getActions('com_foos');` fragt die Aktionen ab, die du zu Beginn dieses Kapitels in der Datei `administrator/components/com_foos/ access.xml` festegelegt hast.

[administrator/components/com_foos/ src/View/Foos/HtmlView.php](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/src/View/Foos/HtmlView.php)

```php {diff}

 \defined('_JEXEC') or die;

+use Joomla\CMS\Helper\ContentHelper;
 use Joomla\CMS\Language\Text;
 use Joomla\CMS\MVC\View\HtmlView as BaseHtmlView;
 use Joomla\CMS\Toolbar\Toolbar;

 	 */
 	protected function addToolbar()
 	{
+		$canDo = ContentHelper::getActions('com_foos');
+
 		// Get the toolbar object instance
 		$toolbar = Toolbar::getInstance('toolbar');

 		ToolbarHelper::title(Text::_('COM_FOOS_MANAGER_FOOS'), 'address foo');

-		$toolbar->addNew('foo.add');
+		if ($canDo->get('core.create'))
+		{
+			$toolbar->addNew('foo.add');
+		}

-		$toolbar->preferences('com_foos');
+		if ($canDo->get('core.options'))
+		{
+			$toolbar->preferences('com_foos');
+		}
 	}
-
 }
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ tmpl/foo/edit.php](https://github.com/astridx/boilerplate/compare/t9...t10#diff-1637778e5f7d1d56dd1751af1970f01b)

Der Eintrag `<?php echo $this->getForm()->renderField(access);` ist notwendig, damit das Feld im Formular aufgenommen wird, welches wir in der XML Datei schon konfiguriert haben. So ist es möglich, die Berechtigungen pro Element zu verändern.

[administrator/components/com_foos/ tmpl/foo/edit.php](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/tmpl/foo/edit.php)

```php {diff}
 <form action="<?php echo Route::_('index.php?option=com_foos&layout=' . $layout . $tmpl . '&id=' . (int) $this->item->id); ?>" method="post" name="adminForm" id="foo-form" class="form-validate">
 	<?php echo $this->getForm()->renderField('name'); ?>
 	<?php echo $this->getForm()->renderField('alias'); ?>
+	<?php echo $this->getForm()->renderField('access'); ?>
 	<input type="hidden" name="task" value="">
 	<?php echo HTMLHelper::_('form.token'); ?>
 </form>
```

<!-- prettier-ignore -->
#### [administrator/components/ com\_foos/ tmpl/foos/default.php](https://github.com/astridx/boilerplate/compare/t9...t10#diff-3186af99ea4e3321b497b86fcd1cd757)

Last but not least nehmen wir für die Anzeige der Berechtigung eine Spalte in der Übersichtliste auf.

[administrator/components/com_foos/ tmpl/foos/default.php](https://github.com/astridx/boilerplate/blob/4efa6681475e12a48143acc126358a0f36fd8452/src/administrator/components/com_foos/tmpl/foos/default.php)

```php {diff}
 								<th scope="col" style="width:1%" class="text-center d-none d-md-table-cell">
 									<?php echo Text::_('COM_FOOS_TABLE_TABLEHEAD_NAME'); ?>
 								</th>
+								<th scope="col" style="width:10%" class="d-none d-md-table-cell">
+									<?php echo TEXT::_('JGRID_HEADING_ACCESS') ?>
+								</th>
 								<th scope="col">
 									<?php echo Text::_('COM_FOOS_TABLE_TABLEHEAD_ID'); ?>
 								</th>

 										<?php echo $editIcon; ?><?php echo $this->escape($item->name); ?></a>

 								</th>
+								<td class="small d-none d-md-table-cell">
+									<?php echo $item->access_level; ?>
+								</td>
 								<td class="d-none d-md-table-cell">
 									<?php echo $item->id; ?>
 								</td>
```

> Beachte, dass ich nicht alle Fälle abdecke, bei denen Berechtigungen zu handhaben sind. Diese Beschreibung ist als exemplarische Vorgehensweise gedacht.

## Teste deine Joomla-Komponente

1. Installiere deine Komponente in Joomla Version 4, um sie zu testen: Kopiere die Dateien im `administrator` Ordner in den `administrator` Ordner deiner Joomla 4 Installation. Installiere deine Komponente wie in Teil eins beschrieben, nachdem du alle Dateien kopiert hast. Joomla aktualisiert bei der Installation die Datenbank für dich.

2. Erstelle in deiner Komponente ein neues Item. Überzeuge dich davon, dass dir ein Auswahlfeld für das Speichern einer Berechtigung angeboten wird. Der Wert, den du hier eingibst, wird mit dem Element gespeichert und ist bei der Anzeige in einer Liste abfragbar.

![Joomla Konfiguration - Berechtigungen in einem Element einstellen](/images/j4x12x1.png)

3. Zur Übersicht wird der Wert zusätzlich in der Hauptansicht angezeigt.

![Joomla Konfiguration - Anzeige der Berechtigungen in der Übersichtsliste](/images/j4x12x2.png)

4. Öffne die Optionen der globalen Konfiguration. Hier hast du die Möglichkeit, die Berechtigungen für die Nutzung der Komponente global zu setzten.

![Joomla Konfiguration - Berechtigungen in der globalen Konfiguration](/images/j4x12x3.png)

5. Spiele mit den Einstellungen. Erlaube einmal nur dem Super Admin, neue Elemente in deiner Erweiterung zu erstellen. Melde dich danach als einfacher Administrator an und überzeuge dich davon, dass die Schaltfläche `New` verschwunden ist.

## Links

[Tutorial zur Zugriffssteuerungsliste (ACL)](https://docs.joomla.org/J3.x:Access_Control_List_Tutorial/de)[^docs.joomla.org/j3.x:access_control_list_tutorial/de]
<img src="https://vg08.met.vgwort.de/na/2c3ca331ccf948289d38a89cfd25b18a" width="1" height="1" alt="">
