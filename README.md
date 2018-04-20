# Statis [![GoDoc](https://godoc.org/github.com/go-mego/static?status.svg)](https://godoc.org/github.com/go-mego/static)

Static 套件可以將靜態檔案、資料夾暴露於指定路由中供客戶端或瀏覽器直接存取。

# 索引

* [安裝方式](#安裝方式)
* [使用方式](#使用方式)
    * [單個檔案](#單個檔案)
        * [多個路由](#多個路由)
    * [檔案系統](#檔案系統)

# 安裝方式

打開終端機並且透過 `go get` 安裝此套件即可。

```bash
$ go get github.com/go-mego/static
```

# 使用方式

透過 `Directory` 可以指定並載入一個目錄，並且傳入 `Serve` 來將其暴露在一個指定的路徑中。

```go
package main

import (
	"github.com/go-mego/mego"
	"github.com/go-mego/static"
)

func main() {
	m := mego.New()
	// 將 `/public` 下的檔案與目錄暴露在 `/` 路徑中。
	m.Use(static.Serve("/", static.Directory("/public")))
	// 暴露檔案目錄時，也可以額外傳入一個選項參數來更改進階設置。
	m.Use(static.Serve("/", static.Directory("/public"), static.Option{
		// 允許這個目錄暴露底下的檔案索引供客戶端瀏覽。
		Indexes: true,
	}))
	m.Run()
}
```

假設 `/public` 裡有 `example.png`，而其目錄暴露到 `/` 路由下，那麼就可以從 `/example.png` 存取 `/public/example.png`。

## 單個檔案

透過 `File` 可以指定單個檔案，並傳入 `Serve` 中暴露。

```go
// 欲暴露的檔案。
img := static.File("/images/example.png")
// 將 `/images/example.png` 檔案暴露為 `/example.png`。
m.Use(static.Serve("/example.png", img))
```

### 多個路由

當使用 Vue.js 或 React.js 撰寫單頁應用程式（SPA）時，通常所有請求都應該要對應到單個 `html` 檔案。為了應付這種情況，可以傳入一個字串切片將多個路由對應到單一檔案。

```go
// 將 `/index.html` 檔案暴露為多個路徑。
// 這會讓不同路由都指向到同個 `/index.html` 檔案。
m.Use(static.Serve([]string{"/user", "/video", "/post"}, static.File("/index.html")))
```

## 檔案系統

以 `ServeFS` 可以暴露一個現有的 `http.FileSystem` 到指定路徑。

```go
// 以 FileSystem 的形式，將 `/images` 目錄暴露在 `/` 路徑中。
m.Use(static.ServeFS("/", http.Dir("/images")))
```