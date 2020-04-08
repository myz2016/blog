# web 中斜杠的不同意义

在 web 中 / 是一种绝对路径。

* / 斜杠 如果被浏览器解析，得到的是：http://ip:port
  * `<a href="/">斜杠</a>`

* 斜杠如果被服务器解析，得到的是：http://ip:port/工程路径
  * `<url-pattern>/servlet</url-pattern>`
  * `servletContext.getRealPath("/")`
  * `request.getRequestDispatcher("/")`
* 特殊情况（重定向）：`response.sendRedirect("/")` 把斜杆发送给浏览器，得到 http://ip:port/

