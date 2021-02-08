---
title: "Frontend-Usage"
_old_id: "925"
_old_uri: "revo/migx/migx.frontend-usage"
---

## Frontend-Usage

## Displaying MIGX Items

MIGX includes a snippet named getImageList that is used to output information from MIGX TVs. Despite the snippet name, non-images can be retrieved as well. Think of it as the ever-popular snippet [getResources](extras/getresources "getResources") but for MIGX.

Here are some sample uses of getImageList:

- image galleries
- image or HTML sliders
- tabular data
- CSV or XML output

Let's get to it!

## Sample Usage

Let's display some images that we input in Step 3. Paste this code wherever you would like to display the images:

``` php
<ul>
  [[getImageList?
    &tvname=`myMIGXtv`
    &tpl=`@CODE:<li>[[+idx]]<img src="[[+image]]"/><p>[[+title]]</p></li>
  `]]
</ul>
```

Let's break this down. The first parameter, `&tvname`, refers to the name of the MIGX TV that we created in Backend Usage, Step 2. `&tpl` refers to either a code string for which to use with the MIGX items or the name of a chunk. If you're using a code string, make sure to prepend the code as above with `@CODE:`.

If you're using [phpthumbof](extras/phpthumbof "phpThumbOf"), you will need to use a chunk and not a code string.

``` php
<ul>
  [[getImageList?
    &tvname=`myMIGXtv`
    &tpl=`thumbTpl`]]
</ul>
```

### thumbTpl

``` php
<li>
  <img src="[[+image:phpthumbof=`w=300&h=300&zc=1`]]" alt="[[+title]]"/>
</li>

```

## Using MIGX with getResources

You can call getImageList from [getResources](extras/getresources "getResources") to build a gallery of galleries.

``` php
<li>
  <a href="[[~[[+id]]]]">[[+pagetitle]]</a>  
  [[getImageList?
    &tvname=`myMIGXtv`
    &tpl=`thumbTpl`
    &limit=`1`
    &docid=`[[+id]]`
  ]]
</li>
```

… and that's that! You now have your very own MIGX image gallery.

### Using getResources values in a MIGX call

If you would like to use getResources values in your chunk called by getImageList, you can do so by including them as parameters in the getImageList snippet call and refer to them by the +property placeholder.

Include them in the snippet call:

``` php
[[getImageList?
    &tvname=`myMIGXtv`
    &tpl=`thumbTpl2`
    &docid=`[[+id]]`
    &limit=`1`
    &pagetitle=`[[+pagetitle]]`
    &originalResourceId=`[[+id]]`
]]

```

and then refer to them in the chunk, prepended with "+property.":

``` php
<li>
  <img src="[[+image:phpthumbof=`w=300&h=300&zc=1`]]" alt="[[+title]]" />
  <a href="[[~[[+property.originalResourceId]]]]">See more images from [[+property.pagetitle]]</a>
</li>
```

Here's another [example from the forum post](http://forums.modx.com/thread/78950/odd-issue-with-migx#dis-post-435072) that lead to the above example.

## Properties

| Name                                                                                                       | Description                                                                                                                                           | Default   |
| ---------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| tvname                                                                                                     | The name of your MIGX-TV                                                                                                                              |           |
| tpl                                                                                                        | The name of the chunk to render each record. You can also use `@CODE:` or `@FILE:`. If empty, getImageList will output an array-string of the records.                |           |
| docid                                                                                                      | Show MIGX-records from other resources. Usefull in [getResources](extras/getresources "getResources")-tpls with `` &docid=`[[+id]]` ``     | `[[*id]]` |
| value                                                                                                      | Send your own JSON-string to getImageList instead of using the TV output. `tvname` and `docid` are ignored.                           |           |
| limit                                                                                                      | If set to non-zero, will only show X number of items.                                                                                                 | 0         |
| offset                                                                                                     | The index to start grabbing from when limiting the number of items.                                                                                   | 0         |
| totalVar                                                                                                   | The key for the total-placeholder, useful together with [getPage](extras/getpage "getPage") for pagination.                                          | total     |
| randomize                                                                                                  | Set `` &randomize=`1` `` if you want randomized output.                                                                                                      | 0         |
| preselectLimit                                                                                             | Together with `&randomize`, this will preselect items from top to limit, for images you want to see in any case in randomized output.                     | 5         |
| where                                                                                                      | Filter items. Example: `{"active:=":"1","rating:>":"5"}`                                                                                              |
| sort                                                                                                       | Sort items by multiple fields. Example: `[{"sortby":"age","sortdir":"DESC","sortmode":"numeric"},{"sortby":"name","sortdir":"ASC"}]`                  |
| reverse                                                                                                    | Set `` &reverse=`1` `` to output everything in reverse order.                                                                                                  | 0         |
| toPlaceholder                                                                                              | Outputs to placeholder. Example: `` &toPlaceholder=`MIGX` `` - get the output by `[[+MIGX]]`                                                                |           |
| toSeparatePlaceholders                                                                                     | Outputs items to seperate placeholders. Example: `` &toSeparatePlaceholders=`MIGX` `` - get the items by `[[+MIGX.1]]`, `[[+MIGX.2]]`, etc.                  |           |
| placeholdersKeyField                                                                                       | Together with `&toSeparatePlaceholders`. Example: `` &placeholdersKeyField=`title` `` - get the items by `[[+MIGX.firsttitle]]`, `[[+MIGX.secondtitle]]`, etc. |           |
| outputSeparator                                                                                            | A seperator between items                                                                                                                             |           |
| toJsonPlaceholder                                                                                          | Output items as json into a placeholder, useful when you want for example show randomized items on different places.                                 |
| example: `` &toJsonPlaceholder=`jsonoutput` `` -> `` [[getImagelist? &value=`[[+jsonoutput]]` ... ]] ``  |                                                                                                                                                       |
| jsonVarKey                                                                                                 | Example: `` &jsonVarKey=`migx_json` `` - will use the value from `$_REQUEST['migx_json']` as value, if any                                           |
| useful together with the backend-preview-feature                                                           | migx\_outputvalue                                                                                                                                     |

## Placeholders

| Placeholder          | Description                                                                                                                                                             |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[[+fieldname]]`     | Replace 'fieldname' with your fieldnames                                                                                                                                |
| `[[+idx]]`           | The index of each item, begins allways with 1                                                                                                                           |
| `[[+_first]]`        | Returns 1, if in first row                                                                                                                                              |
| `[[+_last]]`         | Returns 1, if in last row                                                                                                                                               |
| `[[+_alt]]`          | Returns 1 every second row                                                                                                                                              |
| `[[+total]]`         | Count of all rows, replace 'total' with your totalVar                                                                                                                   |
| `[[+property.name]]` | You can use every script property/param that's set in the actual snippet call, for example if you have `` &docid=`20` `` the placeholder `[[+property.docid]]` will return 20 |

## Advanced Usage

### Switching Template

Using `` &tpl=`@FIELD:` `` you can use any field as the template name to switch template from item to item

``` php
[[getImageList?
  &tvname=`myMIGXtv`
  &tpl=`@FIELD:tpl`
]]
```

If you have specified a field name "tpl" from the MIGX TV setup, getImageList will use the value of this field for the items's tpl. The value must be exactly what you would put in the `&tpl` property - a chunk name, `@CODE:...`, `@FILE:...`.
