# readme-hijacked-by-quest-4-api-in-a-dot-txt.markdown

```js
import got from 'got'
import koa from 'koa'
import cors from 'kcors'
import route from 'koa-route'
import json from 'koa-json'
import jsonbody from 'koa-json-body'
import { exec }  from 'child_process'
import fs from 'fs'

let app = koa()

app.use(function* (next) {
  try {
    yield next
  } catch (err) {
    this.status = err.status || 500
    this.body = { error: err.message }
    this.app.emit('error', err, this)
  }
})
app.on('error', f => console.error(f.stack))

app.use(cors())
app.use(json())
app.use(jsonbody())


let GET = s => handler => app.use(route['get'](s, handler))
let POST = s => handler => app.use(route['post'](s, handler))

// POST `/pod` (function*() {
//   let { name, yaml } = this.request.body
//   if (!name.match(/^[a-z]$/)) throw "invalid name"
//
//   let file = `./pods/${name}.yml`
//   let err = yield (cb => fs.writeFile(file, yaml, cb))()
//   if (err) throw err
//
//   this.body = `curl http://localhost:8080/launch -d '{ "name":"${file}" }'
// `
// })

let __ = (fn, ...x) => cb => fn(...x, cb)

POST `/launch` (function*() {
  let { url } = this.request.body

  let [ data, res ] = yield __(got, url)

  let output = yield __(exec, `kubectl create -f ${url}`)
  if (!output) throw output

  this.body = {
    output: output.trim().split('\n'),
    yaml: data
  }
})

app.listen(8080)
```
