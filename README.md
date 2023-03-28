# blog
# 1.Purpose and Requirements
## 1.1.Purpose
>With the increasing number of bloggers, blogs are becoming more widely accepted as a new lifestyle, work style, and learning tool, and are changing traditional network and social structures. They provide clearer options and directions for communication and collaboration, combine individual ideas with group wisdom more effectively, and make personal publishing a dream that can be achieved by everyone. Blogs are influencing and changing our lives.
>This system uses the SpringMVC pattern to design and implement a blog system. The MVC pattern greatly improves the system's flexibility, reusability, development efficiency, adaptability, and maintainability, fully leveraging the characteristics of J2EE components such as JSP and Servlet. It enables more people to showcase themselves, share their feelings, and participate in communication through text, images, sound, video, wireless, etc., creating a better life for all. Everyone can blog, and everyone needs a blog.

## 1.2.Requirements
>The project practice needs to be developed using the spring+springMVC technology.
The administrator can maintain registered blog users through the front-end page after entering the background management module, including adding, finding, modifying, and deleting registered users.
After logging in, the administrator can manage the account, including adding administrator accounts, modifying administrator accounts, deleting administrator accounts, and setting account permissions.
Blog users can manage their own blog space after logging in to the front end, including publishing their own online diary, collecting personal pictures, communicating with relevant personnel, and deleting comments posted by visitors.
In the specific design and implementation of the blog website, several main functions of mainstream blog websites were mainly considered:
>>(1) Blog registration and login verification function  
>>(2) Keyword search function for blog posts by network users  
>>(3) Recommendation of the most popular blog pages for browsing  
>>(4) Display of detailed article content and related comments  
>>(5) Statistics of blog page views  
>>(6) Management and maintenance functions of personal blog articles  
>>(7) Management and maintenance functions of personal blog article categories  
>>(8) Management and maintenance functions of personal blog friendship links  
>>(9) Management and maintenance functions of personal basic information in blogs  
>>(10) Uploading pictures to the blog and managing personal albums  
>>(11) Network users can leave messages and blog owners can view them.  

# 2.Overall Design Plan
>(1) Building a blog requires two basic data types: user type and article type
>(2) Blog management requires distinguishing between administrators and ordinary users
>(3) Implementing blog functionality requires multiple front-end pages to work together and writing relevant pages for each function
>(4) Decoupling of each module of the blog requires the use of the Spring framework for project development and configuration of relevant settings
>(5) Collaboration in project development requires determination of interfaces for each function and standardization of POJOs.

# 3.Software Flowchart
Due to the large number of processes involved, only the login, management, article publishing, and image uploading processes are displayed.

## 3.1.Login
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E7%99%BB%E5%BD%95.png?raw=true" width="500" height="300" />

## 3.2.Management
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E7%99%BB%E5%BD%95.png?raw=true" width="500" height="300" />

## 3.3.Article Publishing
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E6%96%87%E7%AB%A0%E5%8F%91%E8%A1%A8.png?raw=true" width="500" height="300" />

## 3.4.Article Publishing
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E6%96%87%E7%AB%A0%E5%8F%91%E8%A1%A82.png?raw=true" width="500" height="300" />

## 3.5.Image Uploading
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E5%9B%BE%E7%89%87%E4%B8%8A%E4%BC%A0.png?raw=true" width="500" height="300" />

# 4.Program and Program Segment Function Analysis
I am responsible for the Controller layer in the team. The Controller layer includes admin (administrator control layer) and home (homepage control layer). Among them, admin includes administrator, backend article, article directory, comment, backend link display, backend menu, announcement, basic information, page, backend tag, user, and file upload control layers. Home includes user article control, article query control, comment control, user interface control, sidebar control, announcement display, page details control, and tag control.


Overall flowchart of the control layer:
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E6%8E%A7%E5%88%B6%E5%B1%82.png?raw=true" width="400" height="200" />

## 4.1.Registration, Login, and Backend Jump
### 4.1.1.Registration

<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E6%B3%A8%E5%86%8C.png?raw=true" width="400" height="500" />  

Registration Page:  

As we use the SpringMVC framework, which has a front controller, when sending a request to jump to a page, it always returns a string (as you can see from the URL of the displayed page, there is no JSP at the end). 

```Java
  //Display Registration Page
    @RequestMapping("/register")
    public String registerPage(){
        return "register";
    }
```  

Registration Verification:

```java
 // Registration Verification
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
>Firstly, get three attributes from the request: username, password, and whether to remember the password. Then, use UserService to call the method to determine whether this username exists. If this username exists, put the code and msg (representing error message) into the map. If there is no such username, create a new user and set user information (username, password, status, access, and registration time) for this user. Register this user to the database, put the user in the Session, put the username and password in the Cookie for easy login use. Finally, put success information into the map and convert the map into a JSONObject string format to return (as you can see from @ResponseBody, the returned result is directly put into the Response Body).

>During registration, only the username can be used for registration. However, during login, you can choose to log in using either an email or username.




### 4.1.2.Login
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E7%99%BB%E5%BD%95%E5%89%8D%E7%AB%AF.png?raw=true" width="800" height="450" />

```java
    //Login Page Display:
    @RequestMapping("/login")
    public String loginPage()
    {
        return "login";
    }
```

Login Verification: 

```java
 //Login Verification
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
            map.put("msg","Invalid username!");
        } else if(!user.getUserPass().equals(password)) {
            map.put("code",0);
            map.put("msg","Incorrect password!");
        } else {
            //Login succeeded
            if(user.getUserAccess()==0){
                map.put("code",1);
            }
            else{
                map.put("code",2);
            }
            map.put("msg","");
            //Add session
            request.getSession().setAttribute("user", user);
            //Add cookie
            if(rememberme!=null) {
                //Create two Cookie objects
                Cookie nameCookie = new Cookie("username", username);
                //Set the expiration time of the Cookie to 3 days
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

>This method checks if the user exists based on the username passed in. If the user exists, the login is successful.

>Since users may have different permissions, the page to be redirected to after login will also be different. This check is included in the map returned by the login verification (with different codes for different permissions). When the code is 1, the admin backend page will be opened; when the code is 2, the regular user backend page will be opened.

### 4.1.3.Redirect to Backend
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E8%B7%B3%E8%BD%AC.png?raw=true" width="800" height="450" />

In the AJAX section of the JSP page mentioned in section 2, we can see that after a successful login, different requests will be sent based on the code, which will result in different redirects.
```java
@RequestMapping("/admin")
//Redirect to the main page of the admin backend
public String index(Model model){
    List<Article> articleList=articleService.listRecentArticle(5);
    model.addAttribute("articleList",articleList);

    List<Comment> commentList = commentService.listRecentComment(5);
    model.addAttribute("commentList",commentList);
    return "Admin/AdminIndex";
}

//Redirect to the personal backend page
@RequestMapping("/genUser")
public String userIndex(Model model)
{
    return "User/Article/insert";
}

```
>Since the two types of users have different permissions, their respective backend pages will also display differently. The main page of the admin backend will display the latest articles and comments (passed through the Model). The main page of the regular user backend will be for writing articles.


## 4.2.Article Operations
### 4.2.1.Displaying Article List with Pagination
As per the previous functional requirements, administrators only have two functions for managing articles, i.e., displaying all articles and deleting articles. When clicking on the link for all articles, the "/admin/article" request is sent. In the method mapped to this request, the article list is obtained (the following method is also included in this class).

```java
@Controller
@RequestMapping("/admin/article")
public class AdminArticleController {
    @Autowired
    private ArticleService articleService;
 
    // Display article list in the admin page with delete option.
    // Pagination parameters: pageIndex and pageSize are required.
    @RequestMapping(value = "")
    public String index(@RequestParam(required = false,defaultValue = "1")Integer pageIndex,
                        @RequestParam(required = false,defaultValue = "10")Integer pageSize,
                        @RequestParam(required = false)String status, Model model){
        HashMap<String,Object> criteria=new HashMap<>(1);
        if(status==null)
        {
            // Pass current page index for clicking other pages.
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

To see how pagination works, let's look at the implementation of the "pageArticle" method:

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

### 4.2.2.Deleting Articles  

When the delete button is clicked, the article ID is passed as an AJAX request, which is then used to send the delete request in this AJAX.

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

As we can see, the article ID is passed during transmission, and the delete method in the controller layer looks like this:

```java
    //delete article
    @RequestMapping(value = "/delete/{id}")
    public void deleteArticle(@PathVariable("id")Integer id){
        articleService.deleteArticle(id);
    }
```

## 4.3.Page Display
Displaying Article List: (The first article belongs to another user)
<img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E9%A1%B5%E9%9D%A2%E6%98%BE%E7%A4%BA.png?raw=true" width="800" height="450" />  
  
Clicking the delete button:
  <img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E7%82%B9%E5%87%BB%E5%88%A0%E9%99%A4.png?raw=true" width="400" height="225" />
  
## 4.4.Category Management
### 4.4.1.Displaying Category List
Like article management, all categories are displayed and a pagination plugin is used for display. The logic is similar, so we do not need to trace it back to the mapper layer.

```java
@Controller
@RequestMapping("/admin/category")
public class AdminCategoryController {
    @Autowired
    private CategoryService categoryService;
    @Autowired
    private ArticleService articleService; 
    // Displaying category list (Administrators only have the functionality to display the list and delete it)
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
### 4.4.2.Deleting a Category
This delete request is sent directly via a link.
```java
 // Delete category
    @RequestMapping(value = "/delete/{id}")
    public String deleteCategory(@PathVariable("id")Integer id)
    {
        // Categories with articles cannot be deleted
        int count=articleService.countArticleByCategoryId(id);
        if(count==0)
        {
            categoryService.deleteCategory(id);
        }
        return "redirect:/admin/category";
  }
```

On tracing back to the implementation method of the delete function, we can see that when a category is deleted, the associated records of the category with articles are also deleted.

```java
    @Override
    @Transactional(rollbackFor = Exception.class)
    public void deleteCategory(Integer id) {
        try{
            // As articles and categories are related, deleting in the category requires deleting the associated records in the article-category relationship table
            categoryMapper.deleteCategory(id);
            articleCategoryRefMapper.deleteByCategoryId(id);
        }catch (Exception e){
            e.printStackTrace();
            log.error("Failed to delete category, id:{}, cause:{}", id, e);
            TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
        }
  }

```

### 4.4.3.Page display
Category list display (delete button not available because there are articles under this category)
  <img src="https://github.com/bi9potato/blog/blob/main/ScreenShoot/%E9%A1%B5%E9%9D%A2%E6%98%BE%E7%A4%BA2.png?raw=true" width="800" height="450" />

## 4.5.Tag Management
### 4.5.1.Display Tag List
```java
@Controller
@RequestMapping("/admin/tag")
public class AdminTagController {
    @Autowired
    private ArticleService articleService;
 
    @Autowired
    private TagService tagService;
 
    // Display tag list in admin panel
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

## 4.5.2.Deleting Tags

```java
  //Delete a tag
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

By tracking the implementation method of the delete operation, it can be seen that when deleting a tag, the associated records between articles and tags will be deleted at the same time.

```java
    @Override
    @Transactional(rollbackFor = Exception.class)
    public void deleteTag(Integer id) {
        try {
            tagMapper.deleteById(id);
            articleTagRefMapper.deleteByTagId(id);
        } catch (Exception e) {
            e.printStackTrace();
            log.error("Failed to delete tag, id:{}, cause:{}", id, e);
            TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
        }
    }

```

## 4.6.Page Operations
### 4.6.1.Display all page lists

```java
@Controller
@RequestMapping("/admin/page")
public class PageController {
 
    @Autowired
    private PageService pageService;
 
    // Display all page lists in the admin panel
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

### 4.6.2.Add page
Send the insert request, according to the SpringMVC's DispatcherServlet, to navigate to the added JSP page. There will be an add submission request when confirmed.

```java
// Show the page for adding a page in the admin panel
@RequestMapping(value = "/insert")
public ModelAndView insertPageView()
{
    ModelAndView modelAndView=new ModelAndView();
    modelAndView.setViewName("Admin/Page/insert");
    return modelAndView;
}

// Submit the page for adding
@RequestMapping(value = "/insertSubmit",method = RequestMethod.POST)
public String insertPageSubmit(Page page)
{
    // Check if the page alias already exists
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

### 4.6.3.Delete

```java
// Delete
@RequestMapping(value = "/delete/{id}")
public String deletePage(@PathVariable("id")Integer id)
{
    pageService.deletePage(id);
   return  "redirect:/admin/page";
}

```

### 4.6.4.Edit
First, the page will be queried based on the ID, and this page information will be placed in the Model, which will be displayed on the edit page.

```java
// Edit page
@RequestMapping(value = "/edit/{id}")
public ModelAndView editView(@PathVariable("id")Integer id)
{
    ModelAndView modelAndView = new ModelAndView();

    Page page = pageService.getPageById(id);
    modelAndView.addObject("page", page);
    modelAndView.setViewName("Admin/Page/edit");
    return modelAndView;
}

// Submit the edited page
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
### 4.7.1.Displaying the Link List

```java
@Controller
@RequestMapping("/admin/link")
public class LinkController {
 
    @Autowired
    private LinkService linkService;
 
    // Display the link list in the backend
    @RequestMapping(value = "")
    public ModelAndView linkList(){
        ModelAndView modelAndView = new ModelAndView();
        List<Link> linkList = linkService.listLink(null);
        modelAndView.addObject("linkList", linkList);
        modelAndView.setViewName("Admin/Link/index");
        return modelAndView;
    }

```

### 4.7.2.Adding a Link
Because the add page also displays the link list, all links need to be queried and added to the model.

```java
// Display add link page
    @RequestMapping(value = "/insert")
    public ModelAndView insertLinkView() {
        ModelAndView modelAndView = new ModelAndView();
        List<Link> linkList = linkService.listLink(null);
        modelAndView.addObject("linkList", linkList);
        modelAndView.setViewName("Admin/Link/insert");
        return modelAndView;
    }
 
    // Submitting an added link
    @RequestMapping(value = "/insertSubmit", method = RequestMethod.POST)
    public String insertLinkSubmit(Link link) {
        link.setLinkCreateTime(new Date());
        link.setLinkUpdateTime(new Date());
        link.setLinkStatus(1);
        linkService.insertLink(link);
        return "redirect:/admin/link/insert";
    }

```

### 4.7.3.Editing a Link
Because it is modifying a link, the link information needs to be queried in advance and added to the model to be displayed on the editing page.

```java
 // Edit link
    @RequestMapping(value = "/edit/{id}")
    public ModelAndView editLinkView(@PathVariable("id")Integer id) {
        ModelAndView modelAndView = new ModelAndView();
        Link linkCustom = linkService.getLinkById(id);
        modelAndView.addObject("linkCustom", linkCustom);
 
        List<Link> linkList = linkService.listLink(null);
        modelAndView.addObject("linkList", linkList);
 
        modelAndView.setViewName("Admin/Link/edit");
        return modelAndView;
    }
 
    // Submitting the edited link
    @RequestMapping(value = "/editSubmit", method = RequestMethod.POST)
    public String editSubmit(Link link) {
        link.setLinkUpdateTime(new Date());
        linkService.updateLink(link);
        return "redirect:/admin/link";
    }

```

### 4.7.4.Deleting a Link

```java
 // Delete link
    @RequestMapping(value = "/delete/{id}")
    public String deleteLink(@PathVariable("id")Integer id) {
        linkService.deleteLink(id);
        return "redirect:/admin/link";
    }

```

# 5.Conclusion
>In summary, this blog system consists of three main parts: reading blogs, back-end management, and system management. The back-end management includes article management, comment management, link management, image management, and password management. System management includes account management and password management. This system enables bloggers to better showcase themselves and communicate with each other.

>Due to time constraints, there are some imperfections in the development of the system and the research of the paper. The study of design patterns was not long enough, so writing the code was not as proficient as it could have been, and some help documents were consulted in the process. In detailed design and coding, the most basic development technology was used, the code is simple and easy to understand, and the system's page jumping is relatively clear. The functions of ordinary users and system administrators are separated, and their respective functional operations can obtain the correct data and store it in the database. Additionally, the system utilizes the MVC pattern, which greatly improves the system's flexibility, reusability, development efficiency, adaptability, and maintainability.

>Due to time constraints, there are some shortcomings in the system that need further improvement and refinement.
