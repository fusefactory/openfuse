---
published: false
author: Luca Camellini
tags:
  - openframeworks
  - atom
  - tools
categories:
  - coding
img: 'https://fusefactory.github.io/openfuse/images_posts/atom_w_of_cover.png'
abstract: Tutorial on how to use Atom to run openFrameworks project.
title: Run OF project inside Atom
---
![OF_inside_Atom]({{site.baseurl}}/images_posts/atom_w_of_cover.png)

Downlaod Atom and OF.

Inside your OF project folder digit the follow to open Atom.

```
atom .
```

Press **cmd** + **,** to open Atom **Settings**. Click "**Install**" tab on the left column and search for **gcc-make-run** package and click **Install**.

When the installation is finished search **Makefile** on the left column inside your OF project root. If you don't find it you can generate it with **projectGenerator** app (inside OF). Don't select it but right-click on select **Make Run** from the menu. Note that if the Makefile is selected the menu doesn't show up **Make Run** command.

You can also add a key shortcut: **cmd** + **,** to open Atom **Settings**. Click **Packages** tab on the left column and find **gcc-make-run** package. Click on it and scroll down and find **Keybinding** paragraph. Disable it and click on the icon next to the first row ("f6 gcc-make-run:compile-run .platform-darwin atom-workspace) to copy a code snippet that you need soon.

Now click on **Keybinding** tab on the left column and click on the first row on the **your keymap file** link to open your Atom keybinding customization. Go at the end of the _keymap.cson_ and paste the code snippet you should copy clicking on the icon. It should be something like the follow:

```
'.platform-darwin atom-workspace':
  'f6': 'gcc-make-run:compile-run'
```

The only thing to do is change the 'f6' with what you are comfortable with (I used to use **cmd** + **r** to build and run my OF project from XCode).

Now to Build and Run your project select Makefile clicking on it and press **cmd** + **r**

You need to have all your used addons inside **addons.make** file like:

```
ofxGui
ofxXmlSettings

```

If you use libraries or "package" like MSAOpenCL.framework that you usually add inside XCode as "Linked Frameworks and Libraries", you need to be sure that you have addon_config.mk inside the addons folder.

That's all.
