## DOA

## STRUCTURES
```json
[
  {
    "title_en": "(string) title on english (en) as identifier",
    "title_id": "(string) title on english (id) as identifier",
    "type": "(string) data type purpose for (note|dua)",
    "contents": [
      {
        "pre_text_id": "(string|null) string if there are content otherwise null of pre text before text (arabic)",
        "post_text_id": "(string|null) string if there are content otherwise null of pre text after inline_post_translation_(id) / end of text",
        "text": "(string|null) string arabic text otherwise null",
        "translation_id": "(string|null) translation or notes, id as identifier. (add double quote to text)",
        "inline_pre_translation_id": "(string|null) string or null, put in-line (one line) before translation start",
        "inline_post_translation_id": "(string|null) string or null, put in-line (one line) after translation end"
      }  
    ]
  }
]

```

## SAMPLE

if there are new lines on contents value of string, please add it as list. (ul/li) 

```javascript

// object doa
var doa = [{}];

function convertTo(data, tag, isRTL, attr)
{
    if (!data) {
        return '';
    }
    tag = tag ? tag : 'div';
    attr = attr && typeof attr === 'string' ? attr : '';
    attr = attr.trim();
    var html = isRTL ? '<'+tag+' dir="rtl" '+attr+'>' : '<div>';
        if (data.match(/[\n]/)) {
            var new_data = data.split("\n");
            html += "<ul>";
            for (i = 0;new_data.length > i;i++) {
                html += "<li>"+new_data[i]+"</li>";    
            }
            html += "</ul>";
        } else {
            html += data;
        }
    html += '</'+tag+'>';
    return html; 
}

var allHtmls = [];
for (var rootKey in doa) {
    if (!doa.hasOwnProperty(rootKey)) {
        continue;
    }
    var currentDoa = doa[rootKey];
    if (typeof currentDoa['contents'] === 'undefined' 
        || !currentDoa['contents']
        || currentDoa['contents'] !== 'object'
        || !currentDoa['contents'].length
    ) {
        continue;
    }

    var title = currentDoa['title_id']||null;
    if (!title_id) {
        continue;
    }
    var html = '<h2>'+title+'</h2>'; 
    var currentContents = currentDoa['contents'];
    html += '<div class="content-section">';
    for (var i=0; currentContents.length > i;i++) {
        var current = currentContents[i];
        var pre = current['pre_text_id'];
        var post = current['post_text_id'];
        var text = current['text'];
        var trans = current['translation_id'];
        var inline_pre = current['inline_pre_translation_id'];
        var inline_post = current['inline_post_translation_id'];
        if (pre) {
            // pre is paragraf
            html += convertTo(pre, 'p', false, 'class="pre-text"');
        }
        if (text) {
            // text is div cause it was arabian
            html += convertTo(pre, 'div', true, 'class="arabian-text rtl"');
        }
        var currentTrans = '';
        if (inline_pre) {
            // put pre of translation use span
            currentTrans += convertTo(inline_pre, 'span', false, 'class="pre-trans"') + ' '; // add 1 space
        }
        if (trans) {
              currentTrans += '<em>"'+ convertTo(trans, 'span', false, 'class="trans"') +'"</em>'; // add double quotes  
        }
        if (inline_post) {
            // put post of translation
            currentTrans += ' ' + convertTo(inline_post, 'span', false, 'class="post-trans"') + ' '; // add 1 space before
        }
        if (currentTrans !== '') {
            // if is on trans
            html += convertTo(pre, 'p', false, 'class="translation-info"');
        }
        if (post) {
            // post is paragraf
            html += convertTo(pre, 'p', false, 'class="post-text"');
        }
    }

    html+= '</div>';
    allHtmls.push(html);
}

// console.log(allHtmls);


```

## DESCRIPTION OF DOA LIST KEYS

> `title_`(string|iso code: en|id) : important

Title in display mode, and must be show as heading text

> `contents` (array|list of doa: array[]) : important

List of doa on sections of title.

> Sample Structure:

```html
<div class="section-dua">
    <h1 class="title dua-title">{title_id}</h1>
    <div class="dua-container">
        <!-- LOOP -->

        <!-- if ({pre_text_id} not empty): -->
        <p dir="ltr">{pre_text_id}</p>
        <!-- endif; // {pre_text_id} not empty -->

        <!-- if ({text} not empty): -->
        <p dir="rtl" class="arabic-text">{text}</p>
        <!-- endif; // {text} not empty -->

        <!-- if ({inline_pre_translation_id}|{translation_id}|{inline_post_translation_id} not empty): -->
        <p dir="ltr" class="translation">
            {inline_pre_translation_id}
            {translation_id}
            {inline_post_translation_id}
        </p>
        <!-- endif; // {inline_pre_translation_id}|{translation_id}|{inline_post_translation_id} not empty -->

        <!-- if ({post_text_id} not empty): -->
        <p dir="ltr">{post_text_id}</p>
        <!-- endif; // {post_text_id} not empty -->

        <!-- END LOOP -->
    </div>
</div>
```