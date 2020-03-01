# Rules Folder

Copy the **rules_examples** and create your own **rules** folder. The rules folder represents the **File provider**. By mapping the **File directory** to this folder you can easily manage and organize the dynamic configuration for the File provider. The **watch** flag is on by default, so changes made to these files will update in real-time. You can set the watch flag to false to stop this feature.

It is also possible to create sub-directories here to further organize things, however the watch function doesn't seem to work on files within the sub-directories.

# Acme Folder

Create a file in the **acme** folder called **acme.json**, and assign the correct permissions:

```
$ touch /path/to/acme/acme.json
$ chmod 600 !$
```
