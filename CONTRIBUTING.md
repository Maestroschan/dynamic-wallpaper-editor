# How to contribute

This quite simple project uses Python3 and PyGObject. The build system is
`meson`, and the project can easily be built as a flatpak package, for example
using **GNOME Builder**.

----

## If you find a bug or want a new feature

- If you can, try to check if it hasn't already been fixed.
- Report it with:
	- OS version
	- Flatpak version
	- App version
	- If it's meaningful, screenshots

----

## If you want to translate the app

In the instructions below, `LANG` is your language code typically of the form
`ll` or `ll_CC`. Examples: `es`, `pt_BR`, `it`, `zh_TW`, …

1. Before starting the translation, fork the repo and clone it on your disk.
2. Add your language code to `po/LINGUAS`.
3. Build the app once (using GNOME Builder).
4. Run `ninja -C _build dynamic-wallpaper-editor-update-po` at the root of the
project. It should produce a `.po` file for your language, at `po/LANG.po`.
If you're updating an existing translation, you can run
`./update-translations.sh src_lang LANG` instead.
5. Use a text editor or [an adequate app](https://flathub.org/apps/details/org.gnome.Gtranslator)
to translate the strings of this `.po` file. The string `translator-credits`
should be translated by your name(s), it will be displayed in the "About" dialog.
6. If you want to test your translation: GNOME Builder isn't able to run a
translated version of the app so export it as a `.flatpak` file and install it.
7. Upload your changes to your github account:
```
git add po
git commit
git push
```
8. And submit a "pull request"/"merge request".

## If you want to translate the help manual

1. Same as above.
2. Add your language code to `help/LINGUAS`.
3. Same as above.
<!-- 4. Run `ninja -C _build help-dynamic-wallpaper-editor-update-po` at the root of -->
<!-- the project. It should produce a `.po` file for your language, at `po/LANG.po`. -->
<!-- FIXME ^ -->
4. Try to:
	- Run `ninja -C _build help-dynamic-wallpaper-editor-pot`
	- Create directory for your language running `mkdir help/LANG`
	- Run `msginit -i help/dynamic-wallpaper-editor.pot -o help/LANG/LANG.po`
	- Now your translation file `help/LANG/LANG.po` is ready for translation.
If you're updating an existing translation, you can run
`./update-translations.sh help_lang LANG` instead.
5. Same as above.
6. Same as above.
7. Same as above.
8. Same as above.

<!-- If you want to test your translation, try the following instructions from the -->
<!-- directory where your language is located (e.g. `help/fr/fr.po`): -->

<!-- 1. Replacing `LANG` with your language code, run the command below: -->
<!-- ``` -->
<!-- lang=LANG -->
<!-- msgfmt -co "$lang.mo" "$lang.po" -->
<!-- for page in ../C/*.page; do itstool -m "$lang.mo" -o . "$page"; done -->
<!-- ln -s ../C/legal.xml . -->
<!-- ln -s ../C/figures figures -->
<!-- ``` -->
<!-- 2. See the documentation running: -->
<!-- ``` -->
<!-- yelp index.page -->
<!-- ``` -->
<!-- 3. If satisfied with the translation, submit your `help/LANG/LANG.po` (ignore -->
<!-- the .page files and legal.xml and figures symlinks) -->

----

## If you want to fix a bug or to add a new feature

- The issue has to be reported first. Tell on the issue that you'll do a patch.
- Use tabs in `.py` files, but 2 spaces in `.ui` or `.xml` files.
- In python code, use double quotes for strings the user will see, and single quotes otherwise.
- Concerning design, try to respect the GNOME Human Interface Guidelines as much as possible.
- Concerning the UI, use GMenuModel for all menus.
- Code comments should explain **why** the code is doing what it is doing, **not what** it does.

If you find some bullshit in the code, or don't understand it, feel free to ask
me about it.

### Code content

- `main.py` contains the `Application` class. It manages:
	- CLI options and arguments
	- opening windows
	- application-wide actions, such as the about dialog or the help
	- changing user's settings depending on their desktop environment
		- wallpaper URI
		- wallpaper adjustment
		- lockscreen URI
		- lockscreen adjustment
	- writing files to `~/.var/app/com.github.maoschanz.DynamicWallpaperEditor/config/*.xml`

>The point of writing files to this directory are:

>1. to have a new URI (distinct from the former one) to set, so the desktop
environment understand the change
>2. to avoid permissions issues (otherwise, since we don't have writing
permissions in the home, we don't know where the file is, and only get a
`/run/user/...` path)

>The written file is a copy, the user still has their file where they put it.

- `misc.py` contains general purpose methods, mainly for managing file-chooser
dialogs, and for generic calculations on durations.
- `picture_widget.py` defines row and thumbnail objects. Both have an icon,
a file path, durations and various labels for a given picture. Rows/thumbnails
can be dragged and dropped, or deleted.
- `view.py` manages the list of rows, or the grid of thumbnail (depending on the
user's preference). It provides methods for filtering, sorting, adding, moving,
or removing pictures. It is strongly related to its window.
- `window.py` is the window, with:
	- window-wide actions
	- management of UI elements corresponding to the selected type of wallpaper
	- the spin-buttons used when the type is "slideshow"

### Using GNOME Builder

Clone this repo, open it as a project with GNOME Builder. Don't forget to update
project's dependencies before trying to build the app.

You can then edit the code, and run it (or export it as flatpak) to test your
modifications.

### Using `meson`

```
git clone https://github.com/maoschanz/dynamic-wallpaper-editor.git
cd dynamic-wallpaper-editor
```

And after doing a patch, you can install the app on your system:

```
meson _build
cd _build
ninja
sudo ninja install
```

----

