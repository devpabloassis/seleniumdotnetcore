# Selenium in Docker DotNetCore Chrome
Selenium .NetCore

Docker image : devpassis/seleniumdotnetcore:latest

docker pull devpassis/seleniumdotnetcore

docker run -it -v d:/Path/to/Project/VSCode:/data devpassis/seleniumdotnetcore /bin/bash

Container (Docker):

'# cd \data

'# dotnet test

Example Code:

using System;

using NUnit.Framework;

using OpenQA.Selenium;

using OpenQA.Selenium.Chrome;

namespace VSCode
{
    [TestFixture]
    class Program
    {
        private IWebDriver driver {get; set;}

        [SetUp]
        public void SetupTest()
        {   
            String _url = "http://SitePesquisa";         
            
            //Configuration to Windows
            //String driverPath = @"\Path\To\Driver";
            //String driverExecutableFileName = "chromedriver.exe";

            //Configuration to Linux - Container
            String driverPath = "/opt/selenium/";
            String driverExecutableFileName = "chromedriver";

            //ChromeDriver driver = new ChromeDriver("./");

            ChromeOptions options = new ChromeOptions();
            options.AddArguments("headless");
            options.AddArguments("no-sandbox");
            options.AddArguments("window-size=1200x600");      
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
			      driver.Navigate().GoToUrl(_url);                         
            IWebElement btnClick =driver.FindElement(By.id("ElementIdOfObject"));
            btnClick.Click();            
		
            IWebElement bodyElement = driver.FindElement(By.Id("ElementIdOfObject"));
            String strContent= bodyElement.GetAttribute("textContent");
            Assert.AreEqual("PlanTextExpectedInBodyElement" , strContent);
        }

    }
}



