---
title: 使用原生js实现以下效果：点击容器内的图标，图标边框变成border:1px solide red,点击空白处重置
date: 2023-05-09 10:50:24
tags: CSS
---

<meta name="referrer" content="no-referrer"/>


### 答案

```
const box = document.getElementById('box)

function isIcon(target){
    return target.className.includes('icon')
}

box.onclick = function(e){
    e.stopPropagation()
    const target = e.target
    if(isIcon(target)) {
        target.style.border = '1px solide red'
    }
}

const doc = document;

doc.onclick = function(e){
    const children = box.children
    for(let i = 0; i< children.length; i++) {
        if(isIcon(children[i])) {
            children[i].style.border = 'none'
        }
    }
}
```

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

