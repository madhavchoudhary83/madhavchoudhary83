package com.corestack.mspaccnt.lib.utils;

import java.io.File;
import java.net.URL;
import java.text.DecimalFormat;
import java.time.Duration;
import java.util.List;
import java.util.Random;
import java.util.function.Function;
import org.apache.commons.io.FileUtils;
import org.openqa.selenium.By;
import org.openqa.selenium.ElementNotVisibleException;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.Keys;
import org.openqa.selenium.NoSuchElementException;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.TimeoutException;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.interactions.Actions;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.FluentWait;
import org.openqa.selenium.support.ui.Wait;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.testng.Reporter;

import com.corestack.mspaccnt.lib.contants.TestParameters;
import com.corestack.mspaccnt.lib.exceptions.WebsiteUnavailableException;
import java.lang.Math;

/**
 * @author manali
 *
 *Driver related utils 
 */
public class DriverUtils {
	
	private static final long SLEEP = 5000;
    private static final DecimalFormat df = new DecimalFormat("0.00");


	public static boolean switchtoWindow(WebDriver driver) {
		for (String handle1 : driver.getWindowHandles()) {

			driver.switchTo().window(handle1);
		}
		return true;
	}

	public static void scrollDown(WebDriver driver, By by) {
		JavascriptExecutor jse = (JavascriptExecutor) driver;

		WebElement element = driver.findElement(by);

		// This will scroll the page till the element is found
		jse.executeScript("arguments[0].scrollIntoView();", element);
	}

	public static void scrollDown(WebDriver driver, WebElement element) {
		JavascriptExecutor jse = (JavascriptExecutor) driver;

		jse.executeScript("arguments[0].scrollIntoView();", element);
	}

	public static void sleep(long sleep) {
		try {
			Thread.sleep(sleep);
		} catch (InterruptedException e) {
			// Ignore
		}
	}

	public static void sleep() {
		sleep(SLEEP);
	}

	public static void waitForPageLoad(WebDriver driver) {
		long wait = getWait();

		waitForPageLoad(driver, wait);
	}

	public static void waitForPageLoad(WebDriver driver, long wait) {

		new WebDriverWait(driver, wait);
		JavascriptExecutor js = (JavascriptExecutor) driver;  
		js.executeScript("return document.readyState").equals("complete");
		
	}

	public static void waitForElement(WebDriver driver, By by) {
		long wait = getWait();

		waitForElement(driver, by, wait);
	}

	public static void waitForElement(WebDriver driver, By by, long wait) {
		WebDriverWait iwait = new WebDriverWait(driver, Duration.ofMillis(wait));
		try {		
			iwait.until(ExpectedConditions.visibilityOfElementLocated(by));					
		} catch (Exception e) {
			TestLog.log("" + e);
			throw new ElementNotVisibleException("Element is not visible.");
		}
		
		try {		
			iwait.until(ExpectedConditions.elementToBeClickable(by));					
		} catch (Exception e) {
			TestLog.log("" + e);
			throw new ElementNotVisibleException("Element is not clickable.");
		}
	}
	
	public static void waitForPageTitle(WebDriver driver, String title) {
		long wait = getWait();

		waitForPageTitle(driver, title, wait);
	}

	public static void waitForPageTitle(WebDriver driver, String title, long wait) {
		new WebDriverWait(driver, wait).until(ExpectedConditions.titleContains(title));
	}

	public static boolean checkElement(WebDriver driver, By by) {
		long wait = getWait();

		return checkElement(driver, by, wait);
	}

	public static boolean checkElement(WebDriver driver, By by, long wait) {
		try {
			waitForElement(driver, by, wait);
			return true;
		} catch (TimeoutException e) {
			return false;
		}
	}

	public static boolean checkElement(WebDriver driver, WebElement element) {
		long wait = getWait();

		return checkElement(driver, element, wait);
	}
	
	public static boolean checkElement(WebElement element)
	{
		try 
		{
			element.isDisplayed();
			return true;
		}
		catch (NoSuchElementException e)
		{
			return false;
		}
	}
	public static boolean checkElement(WebDriver driver, WebElement element, long wait) {
		try {
			waitForElement(driver, element, wait);
			return true;
		} catch (TimeoutException e) {
			return false;
		}
	}

	public static void waitForElement(WebDriver driver, WebElement element) {
		long wait = getWait();

		waitForElement(driver, element, wait);
	}

	public static void waitForElement(WebDriver driver, WebElement element, long wait) {
		WebDriverWait iwait = new WebDriverWait(driver, Duration.ofMillis(wait));
		
		try {		
			iwait.until(ExpectedConditions.visibilityOf(element));					
		} catch (Exception e) {
			TestLog.log("" + e);
			throw new ElementNotVisibleException("Element is not visible.");
		}
		
		try {
		
			iwait.until(ExpectedConditions.elementToBeClickable(element));					
		} catch (Exception e) {
			TestLog.log("" + e);
			throw new ElementNotVisibleException("Element is not clickable.");
		}
	}
	
	public static void waitForText(WebDriver driver, WebElement element, String text) {
		long wait = getWait();

		waitForText(driver, element, text, wait);
	}

	public static void waitUntilPageLoads(WebDriver driver, WebElement element) {
		sleep();
		waitForElement(driver, element);
	}
	
	public static void waitUntilElementLoad(WebDriver driver, WebElement element, long sleep) {
		sleep(sleep);
		waitForElement(driver, element);
	}
	
	public static void implicitWait(WebDriver driver, long wait) {
		
		driver.manage().timeouts().implicitlyWait(Duration.ofMillis(wait));
	}
	
	public static void waitUntilElementPresence(WebDriver driver, By by , long sleep) {
		WebDriverWait wait = new WebDriverWait(driver, Duration.ofMillis(sleep));
		try {
			wait.until(ExpectedConditions.presenceOfElementLocated(by));					
		} catch (Exception e) {
			TestLog.log("" + e);
			throw new ElementNotVisibleException("Element is not present.");
		}
	}
	
	public static void refreshPage(WebDriver driver) {
		driver.navigate().refresh();
	}
	
	public static void waitUntilElementLoads(WebDriver driver, final By ByLocator) {
		
		Wait<WebDriver> wait = new FluentWait<WebDriver>(driver)
				.withTimeout(Duration.ofSeconds(TestParameters.THIRTY))
				.pollingEvery(Duration.ofSeconds(TestParameters.TEN))
				.ignoring(NoSuchElementException.class);
		
		 wait.until(new Function<WebDriver, WebElement>() {
				public WebElement apply(WebDriver driver) {
					return driver.findElement(ByLocator);
			}
		});		
	}
	
	public static void waitUntilElementLoad(WebDriver driver, By by , long sleep) {
		sleep(sleep);
		WebDriverWait wait = new WebDriverWait(driver, Duration.ofMillis(sleep));
		try {
			wait.until(ExpectedConditions.visibilityOfElementLocated(by));					
		} catch (Exception e) {
			TestLog.log("" + e);
			throw new ElementNotVisibleException("Element is not clickable.");
		}
	}
	
	public static void waitForText(WebDriver driver, WebElement element, String text, long wait) {
		new WebDriverWait(driver, wait).until(ExpectedConditions.textToBePresentInElement(element, text));
	}

	private static long getWait() {
		return Long.parseLong(AutomationProperties.getProperty("corestack.mspaccount.automation.selenium.page.wait"));
	}

	public static void setAttribute(WebDriver driver, String id, String attribute, String value) {
		String script = "var elm = document.getElementById('" + id + "');\n";
		script += "var att = document.createAttribute('" + attribute + "');\n";
		script += "att.value = '" + value + "';\n";
		script += "elm.setAttributeNode(att); ";
		((JavascriptExecutor) driver).executeScript(script);
	}

	public static void click(WebDriver driver, WebElement element) {
		JavascriptExecutor executor = (JavascriptExecutor) driver;
		executor.executeScript("arguments[0].click();", element);
	}
	public static void captureScreenShots(WebDriver driver)
	{
		 captureScreenShots(driver, TestLog.getTestCase(), TestLog.getScenario());
	}

	/**
	 * capture screenshot of web page and save at specified location
	 * 
	 * @param driver
	 * @param testCase
	 * @param scenario
	 * @return fileName of Screenshot
	 */
	public static void captureScreenShots(WebDriver driver, String testCase, String scenario) {
		try {
			File f = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
			// Setting file name
			String folderName = TestLog.getLogPath();
			
			String fileName = testCase
					+ TestParameters.UNDERSCORE 
					+ "[" + scenario + "]"
					+ System.currentTimeMillis()
					+ ".jpg";
			String filePath = folderName 
					+ File.separator
					+ TestParameters.Screenshots 
					+ File.separator
					+ fileName;

			// copy screenshot file into screenshot folder.
			File destFile = new File(filePath);
			FileUtils.copyFile(f, destFile);
			if (TestParameters.TRUE.equalsIgnoreCase("false"))
			{
				URL url = FTPClientUtils.uploadFile(folderName+ File.separator + TestParameters.Screenshots , fileName);
				TestLog.logScreenshot(url.toString());
			}
			else
			{
				Reporter.log("<a href='"+ destFile.getAbsolutePath() + "'> <img src='"+ destFile.getAbsolutePath() + "' height='100' width='100'/> </a>");
				TestLog.logScreenshot(".." + File.separator + TestParameters.Screenshots + File.separator + fileName);
			}
			
			
			
		} catch (Exception e) {
			TestLog.log(e.getMessage());
//			throw new RuntimeException("Failed to Capture Screenshot (Test Case:" + testCase + " and scenario:"
//					+ scenario + ")due to " + e);
		}
	}
	
	public static void hoverOnOption(WebDriver driver, WebElement option) {
		Actions action = new Actions(driver);
		action.moveToElement(option).build().perform();
		sleep();
		}

	public static void clickOnElement(WebDriver driver, WebElement element) {
		sleep();
		element.click();
		}
	
	public static void setText(WebElement element, String sTextValue, WebDriver driver) {
        ((JavascriptExecutor)driver).executeScript("arguments[0].scrollIntoView();", element);
        element.click();
        element.clear();
        element.sendKeys(sTextValue);
    }
	
	
	public static void scrollWindow(WebDriver driver,int x, int y) {
		JavascriptExecutor js = (JavascriptExecutor) driver;
		js.executeScript("window.scrollBy("+x+","+y+")", "");
	}

	public static boolean isElementPresent(WebElement element, String checkedStr, WebDriver driver) {
		// TODO Auto-generated method stub
		boolean flag = false;
		try {
			DriverUtils.waitForElement(driver, element);
		} catch (Exception e) {
			// TODO: handle exception
		}
		  boolean ischecked  = element.isDisplayed();
		    if (ischecked) {
				  
				     TestLog.logStep(checkedStr + " : Element is displayed");
				     flag = true;
			} else {
                 throw new NoSuchElementException(checkedStr + " : Element is not present");
			}
		    return flag;
	}
	
	
	
	
	public static boolean isElementPresent(By byxpath, String checkedStr, WebDriver driver) {
		// TODO Auto-generated method stub
		boolean flag = false;
		try {
			DriverUtils.waitForElement(driver, byxpath);
		} catch (Exception e) {
			// TODO: handle exception
		}
		  boolean ischecked  = driver.findElement(byxpath).isDisplayed();
		    if (ischecked) {
				  
				     TestLog.logStep(checkedStr + " : Element is displayed");
				     flag = true;
			} else {
                 throw new NoSuchElementException(checkedStr + " : Element is not present");
			}
		    return flag;
	}
	
	
	 public static boolean checkedLabel(WebElement element, String label, WebDriver driver) {
  	   boolean flag = false;
  	   
  	   try {
			DriverUtils.scrollDown(driver, element);
		} catch (Exception e) {
			// TODO: handle exception
		}
  	   
  	   boolean isCheckedLabelPresent = DriverUtils.isElementPresent(element,label,driver);
  	   if (isCheckedLabelPresent) {
			  TestLog.logStep("Label is Present in the screen : " + label);
			   flag = true;
		  }
        return flag;
     }
     
	 
	 public static boolean checkedLabel(By byxpath, String label, WebDriver driver) {
	  	   boolean flag = false;
	  	   
	  	   try {
				DriverUtils.scrollDown(driver, byxpath);
			} catch (Exception e) {

				DriverUtils.waitForElement(driver, byxpath);
				DriverUtils.scrollDown(driver, byxpath);
			}
	  	   
	  	   boolean isCheckedLabelPresent = DriverUtils.isElementPresent(byxpath,label,driver);
	  	   if (isCheckedLabelPresent) {
				  TestLog.logStep("Label is Present in the screen : " + label);
				   flag = true;
			  }
	        return flag;
	     }
	 
	 public static void checkElementtobeClicakble(WebDriver driver, By byxpath) {
		 
		 WebDriverWait wt = new WebDriverWait(driver, 5);
		 wt.until(ExpectedConditions.elementToBeClickable (byxpath));
	 }
	 
public static void checkElementtobeClicakble(WebDriver driver, WebElement element) {
		 
		 WebDriverWait wt = new WebDriverWait(driver, 5);
		 wt.until(ExpectedConditions.elementToBeClickable (element));
	 }

public static int convertStringToInt(String str) {
	  int value = 0; 
	   try {
         value = Integer.parseInt(str);
     }
     catch (NumberFormatException e) {

       throw new NumberFormatException("Value of String can not be converted to integer : " +str);
     }
	   
	    return value;
	   
 }

public static boolean checkedNavigatedPage(String moduleLnk, WebDriver driver) {
	
	
	boolean flgforEnv =   driver.getCurrentUrl().trim().contains(moduleLnk);
	
	   if (flgforEnv) {
		TestLog.logStep("Navigated to this URL :" + driver.getCurrentUrl().trim() );
	} else {
        throw new WebsiteUnavailableException("Website is not navigated to correct URL :" + driver.getCurrentUrl().trim() );
	}
	   return flgforEnv;
   }

public static void click(WebDriver driver, By locators) {
	JavascriptExecutor executor = (JavascriptExecutor) driver;
	executor.executeScript("arguments[0].click();", driver.findElement(locators));
}


public static double convertStringToDouble(String str) {
	  double value = 0; 
	   try {
       value = Double.valueOf(str);
   }
   catch (NumberFormatException e) {

     throw new NumberFormatException("Value of String can not be converted to integer : " +str);
   }
	   
	   return Double.valueOf( df.format(value));
	   
}

 public static void waitforSessionTermination() {
	 
	 sleep(TestParameters.HALF_MINUTE);
	 
 }
 
 	//return ByType of WebElement
	//By format = "[foundFrom] -> locator: term"
		public static By converWebElementToByVal(WebElement we) {

			String[] data = we.toString().split(" -> ")[1].replace("]", "").split(": ");
			String locator = data[0];
			String term = data[1];

			switch (locator) {
			case "xpath":
				return By.xpath(term);
			case "css selector":
				return By.cssSelector(term);
			case "id":
				return By.id(term);
			case "tag name":
				return By.tagName(term);
			case "name":
				return By.name(term);
			case "link text":
				return By.linkText(term);
			case "class name":
				return By.className(term);
			}
			return (By) we;
		}
		
		public static void dragAndDrop(WebDriver driver, WebElement source, WebElement target) {
			Actions builder = new Actions(driver);
			builder.clickAndHold(source).moveToElement(target).release(target).build().perform();
		}
		
		public static int genRandomNum() {
		    Random r = new Random( System.currentTimeMillis() );
		    return ((1 + r.nextInt(2)) * 10000 + r.nextInt(10000));
		}
		
		public static boolean checkElementPresent(WebDriver driver,WebElement element ) {
			By ele = converWebElementToByVal(element);
			TestLog.log("Check if given element is present");
			try {
				List<WebElement> elements = driver.findElements(ele);
				
				if (elements.size() > 0) {
					TestLog.log("Element is present");
					return true;
				} else {
					TestLog.log("Element is not present");
					return false;
				} 
			}catch (Exception e) {
					TestLog.log("Element is not present");
					return false;
			}				
	 }
	
	
	public static boolean checkElementPresent(WebDriver driver,By element) {			
		TestLog.log("Check if given element is present");
		try {
			List<WebElement> elements = driver.findElements(element);
			
			if (elements.size() > 0) {
				TestLog.log("Element is present");
				return true;
			} else {
				TestLog.log("Element is not present");
				return false;
			} 
		} catch (Exception e) {
				TestLog.log("Element is not present");
				return false;
		}
		
    }
	
	public static void clickEscapeKey(WebDriver driver) {
		Actions action = new Actions(driver);
		action.sendKeys(Keys.ESCAPE).build().perform();
	}
	
	public static void clickReturnKey(WebDriver driver) {
		Actions action = new Actions(driver);
		action.sendKeys(Keys.RETURN).build().perform();
	}
	
	public static void waitForElementToDisappear(WebDriver driver, WebElement element, long wait) {
		WebDriverWait iwait = new WebDriverWait(driver, Duration.ofMillis(wait));
		By ele = converWebElementToByVal(element);
		
		try {		
			iwait.until(ExpectedConditions.invisibilityOfElementLocated(ele));					
		} catch (Exception e) {
			TestLog.log("" + e);
			throw new ElementNotVisibleException("Element is still visible.");
		}
	}
	
	
	
	public static void  scrollToTopOfPage(WebDriver driver) {
		
		 JavascriptExecutor js = (JavascriptExecutor) driver;
	       js.executeScript("window.scrollTo(document.body.scrollHeight,0)");
	}
 
}
