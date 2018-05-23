ASP.NET Core Identity - Configure from Scratch!
===================

Hi All, I wanted to configure Identity in an empty **ASP.NET Core web application**. After going through below blogs I was able to successfully configure **ASP.NET Core Identity**. 

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
		npm install -g yo bower
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
 
 
 12. Add `HomeController.cs`


 13. Add Views folder in the root of the web project


 14. In side the Views Folder create the Shared folder


 15. Add the `_Layout.cshtml`


 16. In the Views folder create the `_ViewStart.cshtml` file


 17. Add Home folder in the Views folder


 18. Add `Index.cshtml` file in the Home folder


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

	    public class Program
	    {
		    public static void Main(string[] args)
		    {
		        var host = new WebHostBuilder()
			    .UseKestrel()
			    .UseContentRoot(Directory.GetCurrentDirectory())
			    .UseIISIntegration()
			    .UseStartup<Startup>()
			    .ConfigureAppConfiguration((hostContext, config) =>
			    {
			        // delete all default configuration providers
			        config.Sources.Clear();
			        config.AddJsonFile("appsettings.json", optional: true);
			    })
			    .Build();
    
		        host.Run();
		    }
	    }


23. Add `appsettings.json` file into the web project folder
		
        {
			"ConnectionStrings": {
				"AuthDbConnection": "Data Source=.;Initial Catalog=AuthDb;Integrated Security=True"
            }
        }
24. Add new folder called CustomIdentity. Maintain your custom DbContext of the identity database. Add below implementations as two classes in the that folder
	* ApplicationDbContext
    
            using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
            using Microsoft.EntityFrameworkCore;

            namespace ASRIdentity.Web.CustomIdentity
            {
                public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
                {
                    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
                        : base(options)
                    {
                    }

                    protected override void OnModelCreating(ModelBuilder builder)
                    {
                        base.OnModelCreating(builder);
                        // Customize the ASP.NET Identity model and override the defaults if needed.
                        // For example, you can rename the ASP.NET Identity table names and more.
                        // Add your customizations after calling base.OnModelCreating(builder);
                    }
                }
            }
    
	* ApplicationUser
	
            using Microsoft.AspNetCore.Identity;

            namespace ASRIdentity.Web.CustomIdentity
            {
                // Add profile data for application users by adding properties to the ApplicationUser class
                public class ApplicationUser : IdentityUser
                {
                }
            }
    
25. In order to add mirgrations you need to implement `IDesignTimeDbContextFactory`

          using Microsoft.EntityFrameworkCore;
          using Microsoft.EntityFrameworkCore.Design;
          using Microsoft.Extensions.Configuration;
          using System.IO;

          namespace ASRIdentity.Web.CustomIdentity
          {
              public class ApplicationDesignTimeDbContextFactory : IDesignTimeDbContextFactory<ApplicationDbContext>
              {
                  public ApplicationDbContext CreateDbContext(string[] args)
                  {
                      IConfigurationRoot configuration = new ConfigurationBuilder()
                      .SetBasePath(Directory.GetCurrentDirectory())
                      .AddJsonFile("appsettings.json")
                      .Build();

                      var builder = new DbContextOptionsBuilder<ApplicationDbContext>();

                      var connectionString = configuration.GetConnectionString("AuthDbConnection");

                      builder.UseSqlServer(connectionString);

                      return new ApplicationDbContext(builder.Options);
                  }
              }
          }

26. Add below code peice in the `Startup.cs`

          services.AddDbContext<ApplicationDbContext>(options => 
                      options.UseSqlServer(Configuration.GetConnectionString("AuthDbConnection")));

          services.AddIdentity<ApplicationUser, IdentityRole>()
              .AddEntityFrameworkStores<ApplicationDbContext>()
              .AddDefaultTokenProviders();

27. Add below code peice in the `Startup.cs`

		app.UseAuthentication();

28. Add the following NugetPackage. Due VS issue do not import the NuGet Packages using package manager tool. Unload the project add the below items to project file.

        <ItemGroup>
          <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
          <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        </ItemGroup>
29. Build the solution


30. In the CMD cd into the project root and add migrations and update the database
	
          dotnet ef migrations add Initial
          dotnet ef database update
    

31. Update AccountController
			
		  public class AccountController : Controller
		  {
		 	  private readonly UserManager<ApplicationUser> _userManager;
		 	  private readonly SignInManager<ApplicationUser> _signInManager;
              
		 	  public AccountController(UserManager<ApplicationUser> userManager, SignInManager<ApplicationUser> signInManager)
		 	  {
		 	  	  this._userManager = userManager;
		 	  	  this._signInManager = signInManager;
		 	  }
32. Add Registry GET and POST methods

          public IActionResult Register()
          {
              return View();
          }

          [HttpPost]
          public async Task<IActionResult> Register(string email, string password, string repassword)
          {
              if (password != repassword)
              {
                  ModelState.AddModelError(string.Empty, "Password don't match");
                  return View();
              }

              var newUser = new ApplicationUser
              {
                  UserName = email,
                  Email = email
              };

              var userCreationResult = await _userManager.CreateAsync(newUser, password);
              if (!userCreationResult.Succeeded)
              {
                  foreach (var error in userCreationResult.Errors)
                  {
                      ModelState.AddModelError(string.Empty, error.Description);
                  }

                  return View();
              }

              var emailConfirmationToken = await _userManager.GenerateEmailConfirmationTokenAsync(newUser);
              var tokenVerificationUrl = Url.Action("VerifyEmail", "Account", new { id = newUser.Id, token = emailConfirmationToken }, Request.Scheme);

              ////await _messageService.Send(email, "Verify your email", $"Click <a href=\"{tokenVerificationUrl}\">here</a> to verify your email");

              return this.Redirect("/");
          }
		
33. And now the razor file in `Views/Account/Register.cshtml`
      ```
        @{
            ViewData["Title"] = "Register";
            Layout = "~/Views/Shared/_Layout.cshtml";
        }

        <h2>Register</h2>

        <form method="POST">
            <div>
                <label>Email</label>
                <input type="email" name="email" />
            </div>

            <div>
                <label>Password</label>
                <input type="password" name="password" />
            </div>

            <div>
                <label>Retype password</label>
                <input type="password" name="repassword" />
            </div>

            <input type="submit" />
        </form>

        <div asp-validation-summary="All"></div>
      ```
34. Run the application and try to add a user


35. Check the added user in the database `SELECT * FROM [AuthDb].[dbo].[AspNetUsers]`


36. Add Login functionality in AccountController

```
		public IActionResult Login()
        {
            return View();
        }

        [HttpPost]
        public async Task<IActionResult> Login(string email, string password, bool rememberMe)
        {
            var user = await _userManager.FindByEmailAsync(email);
            if (user == null)
            {
                ModelState.AddModelError(string.Empty, "Invalid login");
                return View();
            }
            if (!user.EmailConfirmed)
            {
                ModelState.AddModelError(string.Empty, "Confirm your email first");
                return View();
            }

            var passwordSignInResult = await _signInManager.PasswordSignInAsync(user, password, isPersistent: rememberMe, lockoutOnFailure: false);
            if (!passwordSignInResult.Succeeded)
            {
                ModelState.AddModelError(string.Empty, "Invalid login");
                return View();
            }

            return Redirect("~/");
        }
```

37. Add login razor view
```

	@{
	    ViewData["Title"] = "Login";
	    Layout = "~/Views/Shared/_Layout.cshtml";
	}

	<h2>Login</h2>
	
	<form method="POST">
	    <div>
	        <label>Email</label>
	        <input type="email" name="email" />
	    </div>
	
	    <div>
	        <label>Password</label>
	        <input type="password" name="password" />
	    </div>
	
	    <input type="submit" />
	</form>

	<div asp-validation-summary="All"></div>
```



Posted by Ashanka Randeniya

Twitter: @AshankaSR
