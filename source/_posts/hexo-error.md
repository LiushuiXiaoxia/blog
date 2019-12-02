---
title: Hexo 命令报错
date: 2019-12-02 20:02:45
categories: Hexo
tags: 
    - Hexo
---

# Hexo 命令报错

---

好友没有写博客了，今天Hexo，发现命令不可用，出现这样的错误`TypeError: Cannot read property 'replace' of null`。

<!-- more -->

```bash
hexo g
INFO  Start processing
INFO  Files loaded in 627 ms
ERROR Render HTML failed: page/2/index.html
TypeError: Cannot read property 'replace' of null
    at Hexo.externalLinkFilter (/Users/xiaqiulei/Documents/blog/node_modules/hexo/lib/plugins/filter/after_render/external_link.js:45:15)
    at Hexo.tryCatcher (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/util.js:16:23)
    at Hexo.<anonymous> (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/method.js:15:34)
    at /Users/xiaqiulei/Documents/blog/node_modules/hexo/lib/extend/filter.js:60:50
    at tryCatcher (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/util.js:16:23)
    at Object.gotValue (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/reduce.js:155:18)
    at Object.gotAccum (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/reduce.js:144:25)
    at Object.tryCatcher (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/util.js:16:23)
    at Promise._settlePromiseFromHandler (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/promise.js:512:31)
    at Promise._settlePromise (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/promise.js:569:18)
    at Promise._settlePromiseCtx (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/promise.js:606:10)
    at _drainQueueStep (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/async.js:142:12)
    at _drainQueue (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/async.js:131:9)
    at Async._drainQueues (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/async.js:147:5)
    at Immediate.Async.drainQueues [as _onImmediate] (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/async.js:17:14)
    at processImmediate (internal/timers.js:439:21)
ERROR Cannot read property 'replace' of null
TypeError: Cannot read property 'replace' of null
    at Hexo.externalLinkFilter (/Users/xiaqiulei/Documents/blog/node_modules/hexo/lib/plugins/filter/after_render/external_link.js:45:15)
    at Hexo.tryCatcher (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/util.js:16:23)
    at Hexo.<anonymous> (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/method.js:15:34)
    at /Users/xiaqiulei/Documents/blog/node_modules/hexo/lib/extend/filter.js:60:50
    at tryCatcher (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/util.js:16:23)
    at Object.gotValue (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/reduce.js:155:18)
    at Object.gotAccum (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/reduce.js:144:25)
    at Object.tryCatcher (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/util.js:16:23)
    at Promise._settlePromiseFromHandler (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/promise.js:512:31)
    at Promise._settlePromise (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/promise.js:569:18)
    at Promise._settlePromiseCtx (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/promise.js:606:10)
    at _drainQueueStep (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/async.js:142:12)
    at _drainQueue (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/async.js:131:9)
    at Async._drainQueues (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/async.js:147:5)
    at Immediate.Async.drainQueues [as _onImmediate] (/Users/xiaqiulei/Documents/blog/node_modules/bluebird/js/release/async.js:17:14)
    at processImmediate (internal/timers.js:439:21)
INFO  0 files generated in 1.6 s
```

跟踪源码到这个文件`/Users/xiaqiulei/Documents/blog/node_modules/hexo/lib/plugins/filter/after_render/external_link.js:45:15`。

```javascript
function externalLinkFilter(data) {
  const { config } = this;

  if (typeof config.external_link === 'undefined' || typeof config.external_link === 'object' ||
    config.external_link === true) {
    config.external_link = Object.assign({
      enable: true,
      field: 'site',
      exclude: ''
    }, config.external_link);
  }
  if (config.external_link === false || config.external_link.enable === false ||
    config.external_link.field !== 'site') return;

  data = data.replace(/<a.*?(href=['"](.*?)['"]).*?>/gi, (str, hrefStr, href) => {
    if (/target=/gi.test(str) || !isExternal(href, config)) return str;

    if (/rel=/gi.test(str)) {
      str = str.replace(/rel="(.*?)"/gi, (relStr, rel) => {
        if (!rel.includes('noopenner')) relStr = relStr.replace(rel, `${rel} noopener`);
        return relStr;
      });
      return str.replace(hrefStr, `${hrefStr} target="_blank"`);
    }

    return str.replace(hrefStr, `${hrefStr} target="_blank" rel="noopener"`);
  });

  return data;
}
```

发现逻辑有问题，修改`_config.yml`，如下所示`enable: false`，即可解决问题。

```yml
external_link: # Open external links in new tab
  enable: false # Open external links in new tab
  field: site # Apply to the whole site
  exclude: ''
```