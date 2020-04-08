# Chain of Responsibility

```java
package com.mfh.filter;

/**
 * @author mfh
 * @date 2020/2/23 10:30
 */
public interface Filter {
    void doFilter(Request req, Response resp, FilterChain chain);
}

```

```java
package com.mfh.filter;

import java.util.ArrayList;
import java.util.List;

/**
 * @author mfh
 * @date 2020/2/23 11:07
 */
public class FilterChain {
    private List<Filter> filters = new ArrayList<>();
    private int index;
    public FilterChain add(Filter filter) {
        this.filters.add(filter);
        return this;
    }

    public void doFilter(Request req, Response resp) {
        if (this.index <= filters.size() - 1) {
            Filter filter = filters.get(index);
            index++;
            filter.doFilter(req, resp, this);
        }
    }
}
```

```java
package com.mfh.filter;

/**
 * @author mfh
 * @date 2020/2/23 10:31
 */
public class Request {
    private String msg;

    public Request(String msg) {
        this.msg = msg;
    }

    @Override
    public String toString() {
        return "Request{" +
                "msg='" + msg + '\'' +
                '}';
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }
}
```



```java
package com.mfh.filter;

/**
 * @author mfh
 * @date 2020/2/23 10:31
 */
public class Response {
    private String msg;

    public Response(String msg) {
        this.msg = msg;
    }

    @Override
    public String toString() {
        return "Response{" +
                "msg='" + msg + '\'' +
                '}';
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }
}
```

```java
package com.mfh.filter;

/**
 * @author mfh
 * @date 2020/2/23 10:32
 */
public class HeadFilter implements Filter {
    @Override
    public void doFilter(Request req, Response resp, FilterChain chain) {
        req.setMsg(req.getMsg().replaceAll("https://www", "http://xxx"));
        System.out.println(req.getMsg());
        chain.doFilter(req, resp);
        resp.setMsg(resp.getMsg() + "-----HeadFilterResponse");
        System.out.println(resp.getMsg());
    }
}
```

```java
package com.mfh.filter;

/**
 * @author mfh
 * @date 2020/2/23 11:16
 */
public class TailFilter implements Filter {
    @Override
    public void doFilter(Request req, Response resp, FilterChain chain) {
        req.setMsg(req.getMsg().replaceAll("com", "cn"));
        System.out.println(req.getMsg());
        chain.doFilter(req, resp);
        resp.setMsg(resp.getMsg() + ">>>>TailFilterResponse");
        System.out.println(resp.getMsg());
    }
}
```

