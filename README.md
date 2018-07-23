# Selenium in Docker with DotNetCore Chrome in Linux and Headless Mode

Note: 

1 - Actually the Chrome in this Conteiner is working just in Mode Headless. So, you won't see the testes running in your linux. 

2 - You can run you test in windows without conteiner, just see the code in VSCode.

Steps:

Step 1:Get the Docker image : devpassis/seleniumdotnetcore:latest. type the command below:

docker pull devpassis/seleniumdotnetcore

Step 2: Run the conteiner with a share path that must existe a VsCode projet:

docker run -it -v d:/Path/to/Project/VSCode:/data devpassis/seleniumdotnetcore /bin/bash

Step3: Inside the Container (Docker). Type the commands:

cd \data 

dotnet test 

Just wait the test execute, and see the result.

If you don't have a project to share  in this container.You need to get or create one. Follow the steps below: /n
--> Create a new Project in Visual Studio Code 

--> Install ths extenstions:
	
	1 - .Net Core Test Explorer
	
	2 - C# for Visual Studio Code
	
--> Add Packages:

	1 - dotnet add package Selenium.WebDriver
	
	2 - dotnet add package NUnit
	
	3 - dotnet add package NUnit3TestAdapter
	
	4 - dotnet add package Selenium.Support
	
	5 - dotnet add package Microsoft.NET.Test.Sdk
	
Note: After package added, If show a alert to restore dependence packages, you will need to accept all of them, to restore de package. You will see that if you copy and paste my code, everything it gonna be ok. This test just acess a url and verify with the text inside the body html is correct. 

using System;

using NUnit.Framework;

using OpenQA.Selenium;

using OpenQA.Selenium.Chrome;

namespace VSCode { 

    [TestFixture]     
    class Program {
         private IWebDriver driver {get; set;}
         private String _url = "http://www.jtable.org/Demo/Filtering";         
	 
    [SetUp]
    
    public void SetupTest()
    {                   
        //Configuration to Windows
        //  String driverPath = @"D:\Projetos\VsCode\SeleniumDotNetCore\bin\Debug\netcoreapp2.1";
        //  String driverExecutableFileName = "chromedriver.exe";         
        //  ChromeOptions options = new ChromeOptions();
        //  options.AddArguments("window-size=1200x600");      
        //Configuration to Linux - Container
        String driverPath = "/opt/selenium/";        
        String driverExecutableFileName = "chromedriver";
        ChromeOptions options = new ChromeOptions();
        options.AddArguments("headless");
        options.AddArguments("no-sandbox");        
        options.BinaryLocation = "/opt/google/chrome/chrome";            
        ChromeDriverService service = ChromeDriverService.CreateDefaultService(driverPath,driverExecutableFileName);         
        driver = new ChromeDriver(service,options,TimeSpan.FromSeconds(30));
        driver.Manage().Timeouts().ImplicitWait = TimeSpan.FromSeconds(40);
        driver.Manage().Window.Maximize();                        
    }
    
    [TearDown]
    
    public void TeardownTest()
    {
        driver.Quit();
    }
    
    [Test]
    
    public void testMethod()
    {   
        try{
	
            this.driver.Navigate().GoToUrl(this._url);                                              
            IWebElement bodyElement = this.driver.FindElement(By.XPath("//div[contains(@class,'main-header')]/h2"));
            String strContent= bodyElement.GetAttribute("textContent");
            Assert.AreEqual("A JQuery plugin to create AJAX based CRUD tables." , strContent);
	    
        }
        catch (Exception ex)
        {
            Console.WriteLine("Error: " + ex.ToString());
        }        
    }
    
}

}
