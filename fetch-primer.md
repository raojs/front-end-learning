
一个简单的POST请求
```js
fetch(URL, {
    method: 'POST',
    headers: {
        "Content-Type": "application/x-www-form-urlencoded"
    },
    body: 'name=jason'
}).then((res) => {
    return res.json();
})
```

- [中文API](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)
