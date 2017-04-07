# api documentation for  [usage (v0.7.1)](https://github.com/arunoda/node-usage)  [![npm package](https://img.shields.io/npm/v/npmdoc-usage.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-usage) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-usage.svg)](https://travis-ci.org/npmdoc/node-npmdoc-usage)
#### simple way to lookup linux process usage

[![NPM](https://nodei.co/npm/usage.png?downloads=true)](https://www.npmjs.com/package/usage)

[![apidoc](https://npmdoc.github.io/node-npmdoc-usage/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-usage_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-usage/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-usage/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-usage/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Arunoda Susiripala",
        "email": "arunoda.susiripala@gmail.com"
    },
    "bugs": {
        "url": "https://github.com/arunoda/node-usage/issues"
    },
    "dependencies": {
        "bindings": "1.x.x",
        "nan": "^2.0.9"
    },
    "description": "simple way to lookup linux process usage",
    "devDependencies": {
        "mocha": "1.x.x"
    },
    "directories": {},
    "dist": {
        "shasum": "25f3106a519550aba41bf6e7685bc9bd533362ff",
        "tarball": "https://registry.npmjs.org/usage/-/usage-0.7.1.tgz"
    },
    "engines": {
        "node": ">= 0.10.x"
    },
    "gitHead": "6dcb60da3036812258f53400e0fc1a38ae25f82d",
    "gypfile": true,
    "homepage": "https://github.com/arunoda/node-usage",
    "keywords": [
        "usage",
        "ps",
        "cpu",
        "ram",
        "memory"
    ],
    "main": "./lib/usage.js",
    "maintainers": [
        {
            "name": "arunoda",
            "email": "arunoda.susiripala@gmail.com"
        }
    ],
    "name": "usage",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git://github.com/arunoda/node-usage.git"
    },
    "scripts": {
        "install": "node-gyp rebuild",
        "test": "mocha"
    },
    "version": "0.7.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module usage](#apidoc.module.usage)
1.  [function <span class="apidocSignatureSpan">usage.</span>clearHistory (pid)](#apidoc.element.usage.clearHistory)
1.  [function <span class="apidocSignatureSpan">usage.</span>lookup (pid, options, callback)](#apidoc.element.usage.lookup)



# <a name="apidoc.module.usage"></a>[module usage](#apidoc.module.usage)

#### <a name="apidoc.element.usage.clearHistory"></a>[function <span class="apidocSignatureSpan">usage.</span>clearHistory (pid)](#apidoc.element.usage.clearHistory)
- description and source-code
```javascript
function clearHistory(pid) {
  if(pid) {
    if(historyCpuUsage[pid]) {
      historyCpuUsage[pid] = null;
    }
  } else {
    historyCpuUsage = {};
  }
}
```
- example usage
```shell
...
usage.lookup(pid, options, function(err, result) {

});
~~~

you can clear history cache too
~~~js
usage.clearHistory(pid); //clear history for the given pid
usage.clearHistory(); //clean history for all pids
~~~
...
```

#### <a name="apidoc.element.usage.lookup"></a>[function <span class="apidocSignatureSpan">usage.</span>lookup (pid, options, callback)](#apidoc.element.usage.lookup)
- description and source-code
```javascript
function lookup(pid, options, callback) {
  if(typeof options == 'function') {
      callback = options;
      options = {};
  }
  options = options || {};

  var uptime;
  getUptime(function(err, value) {
    if(err) {
      callback(err);
    } else {
      uptime = value;
      getProcStat(pid, calculateUsage);
    }
  });

  function calculateUsage(err, stat) {
    if(err) {
      callback(err);
    } else {
      var usageData = {};

      if(historyCpuUsage[pid] && options.keepHistory) {
        var cpuUsage = calculateCpuUsageFromHistory(sysinfo, uptime, stat, historyCpuUsage[pid]);
      } else {
        var cpuUsage = calculateCpuUsage(sysinfo, uptime, stat);
      }

      // memeory info
      usageData.memory = calculateMemoryUsage(sysinfo, stat);
      usageData.memoryInfo = {
        rss: calculateMemoryUsage(sysinfo, stat),
        vsize: calculateVirtualMemoryUsage(sysinfo, stat)
      };

      // cpu info
      usageData.cpu = cpuUsage.pcpu;
      usageData.cpuInfo = {
        pcpu: cpuUsage.pcpu,
        pcpuUser: cpuUsage.pcpuUser,
        pcpuSystem: cpuUsage.pcpuSystem,
        cpuTime: cpuUsage.cpuTime
      };

      if(options.keepHistory) {
        //save totalTime in history
        historyCpuUsage[pid] = {
          timestamp: Date.now(),
          stat: stat,
          uptime: uptime
        };
      }

      callback(null, usageData);
    }
  }
}
```
- example usage
```shell
...
## Example

### Code
~~~js
var usage = require('usage');

var pid = process.pid // you can use any valid PID instead
usage.lookup(pid, function(err, result) {

});
~~~

### Result Object
~~~js
{
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
