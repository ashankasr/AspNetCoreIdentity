ASP.NET Core Identity - Configure from Scratch!
===================

Hi All, I wanted to configure Identity in an empty ASP.NET Core web application. After going through below blogs I was able to successfully configure ASP.NET Core Identity. 

Reference Links

 1. http://www.blinkingcaret.com/2016/11/30/asp-net-identity-core-from-scratch/
 2. https://wildermuth.com/2017/07/06/Program-cs-in-ASP-NET-Core-2-0
 3. https://codingblast.com/entityframework-core-idesigntimedbcontextfactory/

----------
Steps
-------------

 1. Install **Node.js**
 
 2. Check **Node.js** and **npm** were installed
	```
	    node -v
	    npm -v
	```
 
 3. Install **bower**, **yeamon** and **yeamon generator** for **asp.net**
	```
		install -g yo bower
		npm install -g generator-aspnet
	```	 
 4. Create a project using yeamon generator and select **Create an empty web application**
	```
		yo aspnet
	```
 5. New project will be created based on the application name you provided in teh step 4
 
 
 6. Before you open the project through visual studio, open the global.json file and update the SDK version (2.0.0). Then you should be able to open the project without any errors
 
 
 7. Go to the project properties and verify whether the Target Framework is .NET Core 2.0
 
 8. Add below NuGet Packages
	```       
		Microsoft.AspNetCore 2.0.0
		Microsoft.AspNetCore.Mvc 2.0.0
		Microsoft.AspNetCore.StaticFiles 2.0.0
	```	
 9. Open Startup.cs and add the IoC configuration required to use MVC.
		 
         
 10. Also, update the Configure method so that the MVC middleware is added to the pipeline with the default route. We’ve also added the StaticFiles middleware, which has to be before MVC
 	
          public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
          {
              loggerFactory.AddConsole();

              if (env.IsDevelopment())
              {
                  app.UseDeveloperExceptionPage();
              }

              app.UseStaticFiles();
              app.UseMvcWithDefaultRoute();
          }
    
 11. Adding first controller. Create Controllers folder in the root of the web project
 
 
 12. Add HomeController


 13. Add Views folder in the root of the web project


 14. In side the Views Folder create the Shared folder


 15. Add the _Layout.cshtml


 16. In the Views folder create the _ViewStart.cshtml file


 17. Add Home folder in the Views folder


 18. Add Index.cshtml file in the Home folder


 19. Copy the below code into the index file

			<h1>Home</h1>

            @if (User.Identity.IsAuthenticated) 
            {
                <p>User @User.Identity.Name is signed in. <a href="/Account/Logout">Logout</a> </p>

            } 
            else 
            {
                <p>No user is signed in.</p>    
            }
            <div>
                <a href="/Account/Login">Login</a>
            </div>
            <div>
                <a href="/Account/Register">Register</a>
            </div>
            <div>
                <a href="/Account/ForgotPassword">Forgot Password</a>
            </div>

20. Add below NuGet Packages
	```
      Microsoft.EntityFrameworkCore 2.0.0
      Microsoft.AspNetCore.Identity 2.0.0
      Microsoft.AspNetCore.Identity.EntityFrameworkCore 2.0.0
    ```
21. Setup application settings in the start up page
	* Add following property
		
        	public IConfiguration Configuration { get; }
        
	* Create the Startup constructor
	
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }
22. Modify Main Method and add using statement for `Microsoft.Extensions.Configuration;`

23. Add appsettings.json file into the web project folder
