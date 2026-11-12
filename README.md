# Selenium with .NET Core in Docker (Headless Chrome)

> **Legacy project (2018).** It is kept as a record of an early experiment: running Selenium tests with .NET Core inside a Linux container, without a graphical interface. The versions used here (.NET Core 2.1, ChromeDriver 2.40) are no longer supported. For my current work, see [AutomatedTesting_Playwright](https://github.com/devpabloassis/AutomatedTesting_Playwright) and [AutomatedTesting_RobotFramework](https://github.com/devpabloassis/AutomatedTesting_RobotFramework).

The [`Dockerfile`](Dockerfile) builds an image with the .NET Core 2.1 SDK, Google Chrome and ChromeDriver. A test project from the host is mounted into the container and executed with `dotnet test`.

Chrome runs in **headless mode** inside the container, so the browser window is not displayed while the tests run. To watch the browser, run the same project on your own machine, outside the container.

## How to run

1. Pull the image:

   ```bash
   docker pull devpassis/seleniumdotnetcore
   ```

2. Start the container, mounting the folder of your test project at `/data`:

   ```bash
   docker run -it -v /path/to/your/project:/data devpassis/seleniumdotnetcore /bin/bash
   ```

3. Inside the container, run the tests:

   ```bash
   cd /data
   dotnet test
   ```

## Creating a test project

1. Create a .NET Core project in Visual Studio Code, with the **C#** and **.NET Core Test Explorer** extensions.
2. Add the packages:

   ```bash
   dotnet add package Selenium.WebDriver
   dotnet add package Selenium.Support
   dotnet add package NUnit
   dotnet add package NUnit3TestAdapter
   dotnet add package Microsoft.NET.Test.Sdk
   ```

3. Accept the prompt to restore the dependencies.

## Example test

The test opens a page and checks the text of its header. In the container, ChromeDriver is in `/opt/selenium/` and Chrome needs the `headless` and `no-sandbox` arguments.

```csharp
using System;
using NUnit.Framework;
using OpenQA.Selenium;
using OpenQA.Selenium.Chrome;

namespace SeleniumDotNetCore
{
    [TestFixture]
    public class HeaderTest
    {
        private IWebDriver driver;
        private const string Url = "http://www.jtable.org/Demo/Filtering";

        [SetUp]
        public void SetUp()
        {
            var options = new ChromeOptions();
            options.AddArguments("headless", "no-sandbox", "window-size=1280,720");
            options.BinaryLocation = "/opt/google/chrome/chrome";

            var service = ChromeDriverService.CreateDefaultService("/opt/selenium/", "chromedriver");
            driver = new ChromeDriver(service, options, TimeSpan.FromSeconds(30));
            driver.Manage().Timeouts().ImplicitWait = TimeSpan.FromSeconds(10);
        }

        [TearDown]
        public void TearDown() => driver.Quit();

        [Test]
        public void HeaderShowsPluginDescription()
        {
            driver.Navigate().GoToUrl(Url);
            var header = driver.FindElement(By.CssSelector(".main-header h2"));
            Assert.AreEqual("A JQuery plugin to create AJAX based CRUD tables.", header.GetAttribute("textContent"));
        }
    }
}
```

To run the same test on Windows, outside the container, remove the `headless` argument and the `BinaryLocation` line, and point `CreateDefaultService` to the folder that contains `chromedriver.exe`.

## Credits

The Chrome installation in the `Dockerfile` is based on the Dockerfile by Jessie Frazelle.
