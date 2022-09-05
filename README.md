# mykid_html_markdown
Userscript for using importing and exporting html/markdown in MyKids wysiwyg.

Install Tampermonkey, download and activate mykid_html_markdown.user.js in Tampermonkey. 

In MyKid go to Newsletters, Click HTML/MD on top, insert/read html and markdown.

Uses showdown and turndown for MD, and they arnt perfectly compatible, just fyi.

showdown and turndown are included in the file (cache your deps), so the actual script is from line 991 to 1045 and looks like so:

```javascript
(function() {
    'use strict';

    var qs = function(q){
        return document.querySelector(q)
    }

    qs(".left-menu ul").insertAdjacentHTML("beforeend", '<li><a class="HTML_insert" href="#" style="background: #FFCCCC;"><span>HTML/MD</span></a></li>')
    qs(".HTML_insert").addEventListener("click", function(event){
        event.preventDefault()

        var iframe = document.querySelector('.cke_wysiwyg_frame')
        var iframeDocument = iframe.contentDocument || iframe.contentWindow.document
        var mked = CKEDITOR.instances.mykid_editor_id

        if (mked){
            var buttons = qs(".buttonbar")
            var iframeContent = iframeDocument.querySelector('.cke_editable')

            buttons.insertAdjacentHTML("beforebegin", "<p>Insert / read html or markdown</p>")
            buttons.insertAdjacentHTML("beforebegin", "<p>showdown is used for inserting md, turndown for converting it back, there are some incompatibilities</p>")
            buttons.insertAdjacentHTML("beforebegin", "<textarea id='hmlt_editor' style='width: 100%; height: 250px;'></textarea>")
            buttons.insertAdjacentHTML("beforebegin", "<span id='hmlt_editor_down' class='btn btn-primary'>\\insert HTML/</span>")
            buttons.insertAdjacentHTML("beforebegin", "<span id='md_editor_down' class='btn btn-primary'>\\insert MD/</span>")
            buttons.insertAdjacentHTML("beforebegin", "<br>")
            buttons.insertAdjacentHTML("beforebegin", "<span id='hmlt_editor_up' class='btn btn-primary'>/read HTML\\</span>")
            buttons.insertAdjacentHTML("beforebegin", "<span id='md_editor_up' class='btn btn-primary'>/read MD\\</span>")
            buttons.insertAdjacentHTML("beforebegin", "<br>")

            var texarea = qs("#hmlt_editor")

            //html insert
            qs("#hmlt_editor_down").addEventListener("click", function(event){
                mked.setData(texarea.value)
            })

            //html back to textarea
            iframeContent.addEventListener("keyup", function(event){
                texarea.value = mked.getData()
            })
            qs("#hmlt_editor_up").addEventListener("click", function(event){
                texarea.value = mked.getData()
            })

            //markdown
            qs("#md_editor_down").addEventListener("click", function(event){
                var converter = new showdown.Converter({tables: true, tasklists: true, strikethrough: true, emoji: true})
                mked.setData(converter.makeHtml(texarea.value))
            })

            qs("#md_editor_up").addEventListener("click", function(event){
                var turndownService = new TurndownService()
                texarea.value = turndownService.turndown(mked.getData())
            })
        }
    })

})();
```
