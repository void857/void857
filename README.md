import random
import threading
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.chrome.options import Options
import time

class PixmapBot:
    def __init__(self, site_url='https://pixmap.fun', grid_selector='.grid-selector', proxy=None):
        self.site_url = site_url
        self.grid_selector = grid_selector
        self.proxy = proxy

        # Setup Chrome options
        chrome_options = Options()
        if proxy:
            chrome_options.add_argument(f'--proxy-server={proxy}')

        # Initialize the Selenium WebDriver (using Chrome in this example)
        self.driver = webdriver.Chrome(options=chrome_options)  # Make sure ChromeDriver is installed and in PATH
        self.driver.get(self.site_url)
        time.sleep(5)  # Wait for the page to fully load

    def place_random_pixel(self):
        # Wait for the grid to be loaded and find it
        grid = self.driver.find_element(By.CSS_SELECTOR, self.grid_selector)
        
        # Get all the pixel elements (assuming each cell in the grid is clickable)
        pixel_elements = grid.find_elements(By.CSS_SELECTOR, 'canvas > div > div')
        
        if pixel_elements:
            # Select a random pixel from the available pixels
            chosen_pixel = random.choice(pixel_elements)
            
            # Use ActionChains to move to the pixel and click it
            actions = ActionChains(self.driver)
            actions.move_to_element(chosen_pixel).click().perform()
            
            print("Placed a pixel on pixmap.fun!")
        else:
            print("No pixel elements found in the grid.")
    
    def fill_square_with_color(self, coord1, coord2, color='red'):
        """
        Fill a square area defined by two coordinates with random pixels in a chosen color.
        
        :param coord1: Tuple (x1, y1) for the top-left corner.
        :param coord2: Tuple (x2, y2) for the bottom-right corner.
        :param color: The color to place on the grid (could be simulated by the bot selecting a color tool on the site).
        """
        # Find the grid on the page
        grid = self.driver.find_element(By.CSS_SELECTOR, self.grid_selector)
        
        # Get all the pixel elements (assuming each cell in the grid is clickable)
        pixel_elements = grid.find_elements(By.CSS_SELECTOR, 'canvas > div > div')

        if not pixel_elements:
            print("No pixel elements found in the grid.")
            return
        
        # Define the range of coordinates to fill
        x1, y1 = coord1
        x2, y2 = coord2

        for x in range(min(x1, x2), max(x1, x2) + 1):
            for y in range(min(y1, y2), max(y1, y2) + 1):
                index = y * (max(x1, x2) + 1) + x

                if 0 <= index < len(pixel_elements):
                    chosen_pixel = pixel_elements[index]
                    
                    actions = ActionChains(self.driver)
                    actions.move_to_element(chosen_pixel).click().perform()
                    
                    print(f"Placed {color} pixel at ({x}, {y}) on pixmap.fun!")
    
    def close(self):
        self.driver.quit()

def run_bot(proxy, coord1, coord2, color):
    bot = PixmapBot(proxy=proxy)
    bot.fill_square_with_color(coord1, coord2, color)
    bot.close()

# List of proxies (replace with actual proxies)
proxies = [
    'http://proxy1:port',
    'http://proxy2:port',
    'http://proxy3:port',
    # Add more proxies here
]

# Coordinates and color for the square
coord1 = (2, 3)
coord2 = (5, 6)
color = 'blue'

# Start 10 threads for 10 bots
threads = []
for i in range(10):
    proxy = proxies[i % len(proxies)]  # Rotate through the proxy list
    t = threading.Thread(target=run_bot, args=(proxy, coord1, coord2, color))
    t.start()
    threads.append(t)

# Wait for all threads to complete
for t in threads:
    t.join()
