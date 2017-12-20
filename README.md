# -Responsebody-RequestBody

一、预备知识：@RequestMapping
RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。
用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。
复制代码
1 @RequestMapping(value = "/aaa")//类级别,可以没有
2 public class myController {
3     @RequestMapping(value = "/bbb")//方法级别,必须有
4     public String getMyName() {
5         return "myReturn";
6     }
7 }
复制代码
对应的action就是：<form action="aaa/bbb">
返回页面就是myReturn.jsp
  
二、@Responsebody与@RequestBody
@Responsebody 表示该方法的返回结果直接写入 HTTP response body 中
一般在异步获取数据时使用，在使用 @RequestMapping 后，返回值通常解析为跳转路径，
加上 @Responsebody 后返回结果不会被解析为跳转路径，而是直接写入 HTTP response body 中。
比如异步获取json数据，加上 @Responsebody 后，会直接返回json数据。
@RequestBody 将 HTTP 请求正文插入方法中,使用适合的 HttpMessageConverter 将请求体写入某个对象。
function login() {//页面异步请求
    var mydata = '{"name":"' + $('#name').val() + '","id":"'
            + $('#id').val() + '","status":"' + $('#status').val() + '"}';
    $.ajax({
        type : 'POST',
        contentType : 'application/json',
        url : "${pageContext.request.contextPath}/person/login",
        processData : false,
        dataType : 'json',
        data : mydata,
        success : function(data) {
            alert("id: " + data.id + "\nname: " + data.name + "\nstatus: "
                    + data.status);
        },
        error : function() {
            alert('出错了！');
        }
    });
};

@RequestMapping(value = "person/login")
   @ResponseBody
   public Person login(@RequestBody Person person) {//将请求中的mydata写入Person对象中
      return person;//不会被解析为跳转路径，而是直接写入HTTP response body中
   }

三、扩展：@PathVariable获取请求路径变量
function profile() {
    var url = "${pageContext.request.contextPath}/person/profile/";
    var query = $('#id').val() + '/' + $('#name').val() + '/'
            + $('#status').val();
    url += query;
    $.get(url, function(data) {
        alert("id: " + data.id + "\nname: " + data.name + "\nstatus: "
                + data.status);
    });
}
@RequestMapping(value = "person/profile/{id}/{name}/{status}")
@ResponseBody
public Person porfile(@PathVariable int id,@PathVariable String name,@PathVariable boolean status) {
    return new Person(id, name, status);
}
//@RequestMapping(value = "/person/profile/{id}/{name}/{status}")中的{id}/{name}/{status}与@PathVariable int id, @PathVariable String name,@PathVariable boolean status一一对应，按名匹配。
 
