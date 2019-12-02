---
title: 原生实现ES5数组方法
date: 2018-11-14 00:28:48
categories:
	- ES5
tags:
	- 兼容
	- 数组方法
---
###### indexOf、lastIndexOf
```javascript
// indexOf
if(!Array.prototype.indexOf) {
    Array.prototype.indexOf = function(item) {
        for(var i = 0; i < this.length; i++) {
            if(this[i] === item) {
                return i;
            }
        }
        return -1;
    }
}
```
<!-- more -->
```javascript
// lastIndexOf
if(!Array.prototype.lastIndexOf) {
    Array.prototype.lastIndexOf = function(item) {
        for(var i = this.length - 1; i >= 0; i--) {
            if(this[i] === item) {
                return i;
            }
        }
        return -1;
    }
}
```
______________________________________________________
###### forEach、map
```javascript
// forEach
if(!Array.prototype.forEach) {
    Array.prototype.forEach = function(fn) {
        for(var i = 0; i < this.length; i++) {
            fn(this[i],i,this);
        }
    }
}
```
```javascript
// map
if(!Array.prototype.map) {
    Array.prototype.map = function(fn) {
        var result = [];
        for(var i = 0; i < this.length; i++) {
            result.push(fn(this[i],i,this));
        }
        return result;
    }
}
```
______________________________________________________
###### fill
```javascript
// fill
if(!Array.prototype.fill) {
    Array.prototype.fill = function(item) {
        for(var i = 0; i < this.length; i++) {
            this[i] = item;
        }
        return this;
    }
}
```
______________________________________________________
###### some、every
```javascript
// some
if(!Array.prototype.some) {
    Array.prototype.some = function(fn) {
        for(var i = 0; i < this.length; i++) {
            if(fn(this[i],i,arr)) {
                return true;
            }
        }
        return false;
    }
}
```
```javascript
// every
if(!Array.prototype.every) {
    Array.prototype.every = function(fn) {
        for(var i = 0; i < this.length; i++) {
            if(!fn(this[i],i,arr)) {
                return false;
            }
        }
        return true;
    }
}
```
______________________________________________________
###### filter
```javascript
// filter
if(!Array.prototype.filter) {
    Array.prototype.filter = function(fn) {
        var result = [];
        for(var i = 0; i < this.length; i++) {
            if(fn(this[i],i,this)) {
                result.push(this[i]);
            }
        }
        return result;
    }
}
```
______________________________________________________
###### reduce、reduceRight
```javascript
// reduce
if(!Array.prototype.reduce) {
    Array.prototype.reduce = function(fn) {
        var result = this[0];
        for(var i = 1; i < this.length; i++) {
            result = fn(result,this[i],i,arr)
        }
        return result;
    }
}
```
```javascript
// reduceRight
if(!Array.prototype.reduceRight) {
    Array.prototype.reduceRight = function(fn) {
        var result = this[this.length - 1];
        for(var i = this.length - 2; i >= 0; i--) {
            result = fn(result,this[i],i,arr)
        }
        return result;
    }
}
```