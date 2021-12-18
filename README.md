# blog
# 1.项目实践的目的与要求
## 1.1.目的
>随着Blog人数的增加，Blog作为一种新的生活方式、新的工作方式和新的学习方式已经被越来越多的人所接受，并且在改变传统的网络和社会结构。它使交流和沟通更有明确的选择和方向性；单一的思想和群体的智慧结合变得更加有效；个人出版变成人人都可以实现的梦想。Blog正在影响和改变着我们的生活。
>本系统采用SpringMVC模式设计实现了一个博客系统。MVC模式极大地提高了系统的灵活性、复用性、开发效率、适应性和可维护性，充分发挥了JSP、Servlet等J2EE组件的特点。使更多的人们通过文字、图片、声音、视频、无线等，尽情展示自我、分享感受、参与交流，美好你我生活。人人都可以博客，人人都需要博客。

## 1.2.要求
>项目的实践需使用spring+springMVC技术进行开发
>管理员通过前台页面进入后台管理模块后，可对注册的博客用户进行维护，包括对注册用户的添加、查找、修改和删除。
>管理员进入登录后，可对帐户进行管理，包括添加管理员帐户、修改管理员帐户、删除管理员帐户和对帐户进行权限设置。
>博客用户通过前台登录后，可对自己的博客空间进行管理，包括发布自己的网络日志、收藏个人图片、和相关人员进行交流和沟通以及删除访问者发表的评论等。
>在具体设计实现该博客网站时，主要考虑了主流博客网站的几个主要功能:
>>(1)博客的注册、登录验证功能  
>>(2)网络用户通过关键字搜索博文功能  
>>(3)最热门博客页面推荐浏览  
>>(4)文章详细内容及相关评论显示  
>>(5)博客页面访问量统计  
>>(6)博客个人文章管理维护功能  
>>(7)博客个人文章分类管理维护功能  
>>(8)博客个人友情链接维护功能  
>>(9)博客个人基本信息管理维护功能  
>>(10)博客图片上传及个人相册管理  
>>(11)网络用户写留言，博主查看留言。

# 2.总体设计方案
>(1)Blog的搭建需要两个基本的数据类型：用户类型与文章类型  
>(2)对Blog的管理需要对用户进行区分为管理员与普通用户  
>(3)Blog功能的实现需要多个前端页面配合，对每个功能编写相关的页面  
>(4)Blog的各个模块的解耦需要运用Spring框架进行项目开发，并配置相关配置  
>(5)项目开发的合作需要对各个功能接口，对pojo的标准进行确定  

# 3.软件流程图
由于流程过多，仅展示登录、管理、文章发表、图片上传5个流程。

## 3.1.登录
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E7%99%BB%E5%BD%95.png?raw=true" width="500" height="300" />

## 3.2.管理
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E7%99%BB%E5%BD%95.png?raw=true" width="500" height="300" />

## 3.3.文章发表
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E6%96%87%E7%AB%A0%E5%8F%91%E8%A1%A8.png?raw=true" width="500" height="300" />

## 3.4.文章发表
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E6%96%87%E7%AB%A0%E5%8F%91%E8%A1%A82.png?raw=true" width="500" height="300" />

## 3.5.图片上传
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E5%9B%BE%E7%89%87%E4%B8%8A%E4%BC%A0.png?raw=true" width="500" height="300" />

# 4.程序及程序功能段功能分析
我在组内负责Controller层。Controller层包括admin（管理员控制层）和home（主页控制层）。其中，admin包括哟管理员、后端文章、文章目录、评论、后台链接显示、后台菜单、公告、基本信息、页面、后台标签、用户、上传文件控制层。home包括用户文章控制、文章查询控制、评论控制、用户主界面控制、侧边栏控制、公告显示、页面详情控制、标签控制。


控制层总体流程：
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E6%8E%A7%E5%88%B6%E5%B1%82.png?raw=true" width="400" height="200" />

## 4.1.注册，登录以及跳转后台
### 4.1.1.注册

<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E6%B3%A8%E5%86%8C.png?raw=true" width="400" height="500" />  

注册页面：  
因为我们使用了SpringMVC框架，这个框架有前端控制器，所以在发送一个请求跳转到页面的时候，都是返回字符串（你可以看到下面页面显示URL的时候，并没有后面的JSP）。  

```Java
  //注册页面显示
    @RequestMapping("/register")
    public String registerPage(){
        return "register";
    }
```  

注册验证：  
```java
 //注册验证
    @RequestMapping(value = "/registerVerify",method = RequestMethod.POST)
    @ResponseBody
    public String registerVerify(HttpServletRequest request,HttpServletResponse response){
        String username = request.getParameter("username");
        System.out.println(username);
        String password = request.getParameter("password");
        String rememberme = request.getParameter("rememberme");
        User user=userService.getUserByName(username);
        Map<String,Object> map=new HashMap<>();
        if(user!=null)
        {
            map.put("code",0);
            map.put("msg","用户已经存在");
        }
        else{
            User insertUser=new User();
            insertUser.setUserName(username);
            insertUser.setUserPass(password);
            insertUser.setUserRegisterTime(new Date());
            insertUser.setUserStatus(1);
            insertUser.setUserAccess(1);
            request.getSession().setAttribute("user", user);
            if(rememberme!=null){
                //创建两个Cookie对象
                Cookie nameCookie = new Cookie("username", username);
                //设置Cookie的有效期为3天
                nameCookie.setMaxAge(60 * 60 * 24 * 3);
                Cookie pwdCookie = new Cookie("password", password);
                pwdCookie.setMaxAge(60 * 60 * 24 * 3);
                response.addCookie(nameCookie);
                response.addCookie(pwdCookie);
            }
            userService.insertUser(insertUser);
            map.put("code",1);
            map.put("msg","用户创建成功");
        }
        String result = new JSONObject(map).toString();
        return result;
}
```  
>首先从发送过来的request请求获得三个属性：用户名，密码和是否记住密码。然后通过UserService调用方法来判断这个用户名是否存在，这个用户名存在的话，就向map放入code和msg（表示错误信息）。如果没有这个用户名的话，就创建新用户，并为这个用户设置用户信息（用户名，密码，状态，权限，注册时间）。将这个用户注册到数据库中，将user放入Session中，将用户名和密码放入Cookie中，方便登录使用。最后将map中放入成功信息，并将map转换成JSONObject的字符串形式返回（看到上面的@ResponseBody,就是将返回结果直接放到Response Body中）。
>这个注册成功后，会将成功信息返回到JSP页面，然后在Ajax部分，判断数据代码，如果成功的话，会跳转到登录页面，不成功的话，就会向下面一样显示注册失败的信息。
注册的时候，只能使用用户名进行注册。但是登录的时候可以选择使用Email和用户名进行登录。




### 4.1.2.登录
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E7%99%BB%E5%BD%95%E5%89%8D%E7%AB%AF.png?raw=true" width="800" height="450" />

```java
    //登录页面显示
    @RequestMapping("/login")
    public String loginPage()
    {
        return "login";
}
```

登录验证：  

```java
 //登录验证
    @RequestMapping(value = "/loginVerify",method = RequestMethod.POST)
    @ResponseBody
    public String loginVerify(HttpServletRequest request, HttpServletResponse response)  {
        Map<String, Object> map = new HashMap<String, Object>();
 
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        String rememberme = request.getParameter("rememberme");
        User user = userService.getUserByNameOrEmail(username);
        if(user==null) {
            map.put("code",0);
            map.put("msg","用户名无效！");
        } else if(!user.getUserPass().equals(password)) {
            map.put("code",0);
            map.put("msg","密码错误！");
        } else {
            //登录成功
            if(user.getUserAccess()==0){
                map.put("code",1);
            }
            else{
                map.put("code",2);
            }
            map.put("msg","");
            //添加session
            request.getSession().setAttribute("user", user);
            //添加cookie
            if(rememberme!=null) {
                //创建两个Cookie对象
                Cookie nameCookie = new Cookie("username", username);
                //设置Cookie的有效期为3天
                nameCookie.setMaxAge(60 * 60 * 24 * 3);
                Cookie pwdCookie = new Cookie("password", password);
                pwdCookie.setMaxAge(60 * 60 * 24 * 3);
                response.addCookie(nameCookie);
                response.addCookie(pwdCookie);
            }
            user.setUserLastLoginTime(new Date());
            user.setUserLastLoginIp(getIpAddr(request));
            userService.updateUser(user);
 
        }
        String result = new JSONObject(map).toString();
        return result;
}
```

>这个就是根据传递过来的username判断是否有这个用户，如果有的话，就登录成功。
>因为登录的用户的权限可能不同，所以跳转的页面也不同，这个判断就放在了登录验证的map中（不同权限的code不同），code为1的返回给Ajax时，会打开管理员后台页面，code为2时，会打开一般用户后台页面。

### 4.1.3.跳转后台
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E8%B7%B3%E8%BD%AC.png?raw=true" width="800" height="450" />
我们可以在第二节的JSP页面的Ajax部分，发现登录成功后，会根据code发送不同的请求，这两个请求会进行不同的跳转。
```java
    @RequestMapping("/admin")
    //转到管理员后台主页
    public String index(Model model){
        List<Article> articleList=articleService.listRecentArticle(5);
        model.addAttribute("articleList",articleList);
 
        List<Comment> commentList = commentService.listRecentComment(5);
        model.addAttribute("commentList",commentList);
        return "Admin/AdminIndex";
    }
 
    //转到个人的后台页面
    @RequestMapping("/genUser")
    public String userIndex(Model model)
    {
        return "User/Article/insert";
    }
```
>因为两个权限不同，页面显示也不会相同。管理员后台主页面会显示最新的文章和最新的评论（放到Model中传递）。一般用户的主页面就是写文章。


## 4.2.文章操作
### 4.2.1.显示文章列表，带有分页功能
在之前的功能划分中，管理员对文章只有显示所有人文章列表和删除文章两个功能。可以看到在点击全部文章链接时，会发送"/admin/article"请求。在这个请求映射的方法中，会获得文章列表(下面的方法也在这个类中）。
```java
@Controller
@RequestMapping("/admin/article")
public class AdminArticleController {
    @Autowired
    private ArticleService articleService;
 
    //后台文章列表显示（这个是管理员的页面，只是显示文章列表和进行删除）
    //这个参数有分页操作必须有的PageIndex,pageSize，只有满足了这两个条件才能满足分页查询
    @RequestMapping(value = "")
    public String index(@RequestParam(required = false,defaultValue = "1")Integer pageIndex,
                        @RequestParam(required = false,defaultValue = "10")Integer pageSize,
                        @RequestParam(required = false)String status, Model model){
        HashMap<String,Object> criteria=new HashMap<>(1);
        if(status==null)
        {
           //会传递现在的页码（方便点击其他页码进行查询）
            model.addAttribute("pageUrlPrefix","/admin/article?pageIndex");
        }
        else
        {
            criteria.put("status",status);
            model.addAttribute("pageUrlPrefix", "/admin/article?status=" + status + "&pageIndex");
        }
        PageInfo<Article> articlePageInfo=articleService.pageArticle(pageIndex,pageSize,criteria);
        model.addAttribute("pageInfo",articlePageInfo);
        return "Admin/Article/index";
    }
...
}
```
为了看到这个分页是如何进行的， 所以追踪到这个pageArticle的实现：
```java
   @Override
    public PageInfo<Article> pageArticle(Integer pageIndex,
                                         Integer pageSize,
                                         HashMap<String, Object> criteria) {
        PageHelper.startPage(pageIndex, pageSize);
        List<Article> articleList = articleMapper.findAll(criteria);
        for (int i = 0; i < articleList.size(); i++) {
            //封装CategoryList
            List<Category> categoryList = articleCategoryRefMapper.listCategoryByArticleId(articleList.get(i).getArticleId());
            if (categoryList == null || categoryList.size() == 0) {
                categoryList = new ArrayList<>();
                categoryList.add(Category.Default());
            }
            articleList.get(i).setCategoryList(categoryList);
        }
        return new PageInfo<>(articleList);
}
```
4.2.2.删除文章
我们可以看到这个点击删除按钮后，会以Ajax的形式传递文章ID，然后在这个ajax中发送删除请求。
```java
//删除文章
function deleteArticle(id) {
    if(confirmDelete()==true){
        $.ajax({
            async: false,
            type: "POST",
            url:'/admin/article/delete/'+id,
            contentType : "application/x-www-form-urlencoded; charset=utf-8",
            dataType: "text",
            complete:function () {
                window.location.reload();
            }
        })
    }
}
```
可以看到这个传递的时候会带有文章ID，Controller层的删除方法：
```java
    //删除文章
    @RequestMapping(value = "/delete/{id}")
    public void deleteArticle(@PathVariable("id")Integer id){
        articleService.deleteArticle(id);
    }
```
## 4.3.页面显示
文章列表显示：（第一篇文章属于另一个用户）
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E9%A1%B5%E9%9D%A2%E6%98%BE%E7%A4%BA.png?raw=true" width="800" height="450" />  
  
点击删除按钮：
  <img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E7%82%B9%E5%87%BB%E5%88%A0%E9%99%A4.png?raw=true" width="800" height="450" />
  
## 4.4.分类管理
### 4.4.1.显示分类列表
和文章的处理一样，都是所有分类裂变，使用分页插件进行显示。逻辑基本一样，不需要追溯到Mapper层。
```java
@Controller
@RequestMapping("/admin/category")
public class AdminCategoryController {
    @Autowired
    private CategoryService categoryService;
    @Autowired
    private ArticleService articleService; 
    //分类列表显示（管理员只有列表显示和删除)
    @RequestMapping(value = "")
    public ModelAndView categoryList(){
        ModelAndView modelAndView=new ModelAndView();
        List<Category> categoryList=categoryService.listCategoryWithCount();
        modelAndView.addObject("categoryList",categoryList);
        modelAndView.setViewName("Admin/Category/index");
        return modelAndView;
    }
...
}
```
### 4.4.2.删除分类
这个删除的请求直接是链接发送请求。
```java
 //删除分类
    @RequestMapping(value = "/delete/{id}")
    public String deleteCategory(@PathVariable("id")Integer id)
    {
        //明确是不可以删除有文章的分类
        int count=articleService.countArticleByCategoryId(id);
        if(count==0)
        {
            categoryService.deleteCategory(id);
        }
        return "redirect:/admin/category";
 
 
}
```
追踪到删除的实现方法，就会发现删除这个分类的同时，会删除这个分类与文章的关联记录。
```java
    @Override
    @Transactional(rollbackFor = Exception.class)
    public void deleteCategory(Integer id) {
        try{
            //因为文章和分类有关，所以在分类中删除，也要在文章和分类关联的表里删除
            categoryMapper.deleteCategory(id);
            articleCategoryRefMapper.deleteByCategoryId(id);
        }catch (Exception e){
            e.printStackTrace();
            log.error("删除分类失败, id:{}, cause:{}", id, e);
            TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
        }
}
```
### 4.4.3.页面显示
分类列表显示（因为这个分类下有文章，所以没有删除按钮）
  <img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E9%A1%B5%E9%9D%A2%E6%98%BE%E7%A4%BA2.png?raw=true" width="800" height="450" />

## 4.5.标签管理
### 4.5.1.显示标签列表
```java
@Controller
@RequestMapping("/admin/tag")
public class AdminTagController {
    @Autowired
    private ArticleService articleService;
 
    @Autowired
    private TagService tagService;
 
    //后台列表显示
    @RequestMapping(value = "")
    public ModelAndView index(){
        ModelAndView modelAndView =new ModelAndView();
        List<Tag> tagList=tagService.listTagWithCount();
        modelAndView.addObject("tagList",tagList);
        modelAndView.setViewName("Admin/Tag/index");
        return modelAndView;
    }
...
}
```
## 4.5.2.删除标签
```java
 //删除标签
    @RequestMapping(value = "/delete/{id}")
    public String deleteTag(@PathVariable("id")Integer id){
        Integer count=articleService.countArticleByTagId(id);
        if(count==0)
        {
            tagService.deleteTag(id);
        }
        return "redirect:/admin/tag";
}
```
追踪到删除的实现方法，可以发现在删除标签的同时会删除文章与标签的关联记录。
```java
    @Override
    @Transactional(rollbackFor = Exception.class)
    public void deleteTag(Integer id) {
        try {
            tagMapper.deleteById(id);
            articleTagRefMapper.deleteByTagId(id);
        } catch (Exception e) {
            e.printStackTrace();
            log.error("删除标签失败, id:{}, cause:{}", id, e);
            TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
        }
}
```
## 4.6.页面操作
### 4.6.1.显示所有页面列表
```java
@Controller
@RequestMapping("/admin/page")
public class PageController {
 
    @Autowired
    private PageService pageService;
 
    //后台列表显示
    @RequestMapping(value = "")
    public ModelAndView index()
    {
        ModelAndView modelAndView=new ModelAndView();
        List<Page> pageList=pageService.listPage(null);
        modelAndView.addObject("pageList",pageList);
        modelAndView.setViewName("Admin/Page/index");
        return modelAndView;
    }
...
}
```
### 4.6.2.添加页面
发送insert请求，根据SpringMVC的DispatcherServlet转到添加的JSP页面。确认添加就会有添加提交的请求。
```java
  //后台添加页面显示
    @RequestMapping(value = "/insert")
    public ModelAndView insertPageView()
    {
        ModelAndView modelAndView=new ModelAndView();
        modelAndView.setViewName("Admin/Page/insert");
        return modelAndView;
    }
 
    //提交添加
    @RequestMapping(value = "/insertSubmit",method = RequestMethod.POST)
    public String insertPageSubmit(Page page)
    {
        //判断页面别名存在
        Page checkPage=pageService.getPageByKey(null,page.getPageKey());
        if(checkPage==null)
        {
            page.setPageCreateTime(new Date());
            page.setPageUpdateTime(new Date());
            page.setPageStatus(PageStatus.NORMAL.getValue());
            pageService.insertPage(page);
        }
        return "redirect:/admin/page";
}
```
### 4.6.3.删除
```java
  //删除
    @RequestMapping(value = "/delete/{id}")
    public String deletePage(@PathVariable("id")Integer id)
    {
        pageService.deletePage(id);
       return  "redirect:/admin/page";
}
```
### 4.6.4.编辑
首先会根据ID查询页面，将这个页面信息放在Model中，这个信息会显示在编辑页面。
```java
  //编辑页面
    @RequestMapping(value = "/edit/{id}")
    public ModelAndView editView(@PathVariable("id")Integer id)
    {
        ModelAndView modelAndView = new ModelAndView();
 
        Page page = pageService.getPageById(id);
        modelAndView.addObject("page", page);
        modelAndView.setViewName("Admin/Page/edit");
        return modelAndView;
    }
 
    //编辑页面提交
    @RequestMapping(value = "editSubmit",method = RequestMethod.POST)
    public String editPageSubmit(Page page)
    {
        Page checkPage=pageService.getPageByKey(null,page.getPageKey());
        if(Objects.equals(checkPage.getPageId(),page.getPageId())){
            page.setPageUpdateTime(new Date());
            pageService.updatePage(page);
        }
        return  "redirect:/admin/page";
}
```
## 4.7.LinkController
### 4.7.1.显示链接列表
```java
@Controller
@RequestMapping("/admin/link")
public class LinkController {
 
    @Autowired
    private LinkService linkService;
 
    //后台链接列表显示
    @RequestMapping(value = "")
    public ModelAndView linkList(){
        ModelAndView modelAndView=new ModelAndView();
        List<Link> linkList=linkService.listLink(null);
        modelAndView.addObject("linkList",linkList);
        modelAndView.setViewName("Admin/Link/index");
        return modelAndView;
}
```
### 4.7.2.添加链接
因为添加页面还会显示链接列表，所以还需要查询所有链接放在Model中。
```java
//添加链接页面显示
    @RequestMapping(value = "/insert")
    public  ModelAndView insertLinkView(){
        ModelAndView modelAndView=new ModelAndView();
        List<Link> linkList=linkService.listLink(null);
        modelAndView.addObject("linkList",linkList);
        modelAndView.setViewName("Admin/Link/insert");
        return modelAndView;
    }
 
    //添加链接提交
    @RequestMapping(value = "/insertSubmit",method = RequestMethod.POST)
    public String insertLinkSubmit(Link link)
    {
        link.setLinkCreateTime(new Date());
        link.setLinkUpdateTime(new Date());
        link.setLinkStatus(1);
        linkService.insertLink(link);
        return "redirect:/admin/link/insert";
}
```
### 4.7.3.编辑链接
因为是对一个链接进行修改，所以会提前查询这个链接信息放在Model中，显示在编辑页面。
```java
 //编辑链接
    @RequestMapping(value = "/edit/{id}")
    public ModelAndView editLinkView(@PathVariable("id")Integer id){
        ModelAndView modelAndView=new ModelAndView();
        Link linkCustom=linkService.getLinkById(id);
        modelAndView.addObject("linkCustom",linkCustom);
 
        List<Link> linkList=linkService.listLink(null);
        modelAndView.addObject("linkList",linkList);
 
        modelAndView.setViewName("Admin/Link/edit");
        return modelAndView;
    }
 
    //编辑链接提交
    @RequestMapping(value = "/editSubmit",method = RequestMethod.POST)
    public String editSubmit(Link link)
    {
        link.setLinkUpdateTime(new Date());
        linkService.updateLink(link);
        return "redirect:/admin/link";
}
```
### 4.7.4.删除链接
```java
 //删除链接
    @RequestMapping(value = "/delete/{id}")
    public String deleteLink(@PathVariable("id")Integer id){
        linkService.deleteLink(id);
        return "redirect:/admin/link";
}
```
# 5.总结
>作为一个完整的博客系统，本系统的主要结构有：阅读博客,后台管理,系统管理。其中,后台包括文章管理,评论管理,链接管理,图片管理,密码管理。系统管理包括帐户管理和密码管理。从而使得博客用户能够更好地展现自我和互相交流。
>系统的开发以及论文的研究由于时间的关系，有一些地方做的还不是很完美。对设计模式的研究时间不长，所以在写代码的时候还没有做到真正的得心应手，是在查询一些帮助文档的情况下才完成的。
在详细设计和编码中，运用最基本的开发技术，代码简单易懂，系统里间跳转比较清晰。完成了普通用户和系统管理员两者的功能分离，而且各自的功能操作都能得到正确数据并存入数据库中保存。另外系统利用MVC模式极大地提高了系统的灵活性、复用性、开发效率、适应性和可维护性。
>由于时间仓促，该系统存在一些不足之处，有待进一步改进和完善。
