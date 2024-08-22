from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
import time

class PixmapBot:
    #... (rest of your class code)

    def inject_ui(self):
        # HTML content
        ui_html = '''
        <div id="bot-control-panel" style="position: fixed; top: 10px; right: 10px; width: 250px; background: rgba(0, 0, 0, 0.8); color: white; padding: 10px; border-radius: 8px; z-index: 10000;">
            <h3 style="margin-top: 0; text-align: center;">Pixel Bot Control</h3>
            <label for="coords1">Start Coords:</label>
            <input type="text" id="coords1" placeholder="x1,y1" style="width: 100%; margin-bottom: 5px;">
            
            <label for="coords2">End Coords:</label>
            <input type="text" id="coords2" placeholder="x2,y2" style="width: 100%; margin-bottom: 5px;">
            
            <label for="color">Color:</label>
            <input type="text" id="color" placeholder="Color" style="width: 100%; margin-bottom: 5px;">
            
            <button id="start-bot" style="width: 100%; background-color: #4CAF50; color: white; border: none; padding: 10px; border-radius: 5px; cursor: pointer;">Start Bot</button>
            
            <button id="stop-bot" style="width: 100%; background-color: #f44336; color: white; border: none; padding: 10px; border-radius: 5px; cursor: pointer; margin-top: 5px;">Stop Bot</button>
        </div>
        '''
        # Injecting the UI into the page
        self.driver.execute_script(f"document.body.innerHTML += `{ui_html}`;")
        
        # JavaScript for functionality
        js_script = '''
        document.getElementById("start-bot").onclick = function() {
            var coords1 = document.getElementById("coords1").value.split(',');
            var coords2 = document.getElementById("coords2").value.split(',');
            var color = document.getElementById("color").value;

            if (coords1.length == 2 && coords2.length == 2 && color) {
                // Start the bot with the given parameters
                startBot(coords1, coords2, color);
            } else {
                alert("Please fill in all fields correctly.");
            }
        };

        document.getElementById("stop-bot").onclick = function() {
            stopBot();  // Function to stop the bot
        };

        function startBot(coords1, coords2, color) {
            console.log(`Starting bot from (${coords1}) to (${coords2}) with color ${color}`);
            // Call the Python bot method using WebDriver's execute_script
            window.coords1 = coords1;
            window.coords2 = coords2;
            window.color = color;
        }

        function stopBot() {
            console.log("Stopping the bot");
            // Stop the bot code here
        }
        '''
        self.driver.execute_script(js_script)

# Inject the UI after opening the page
bot = PixmapBot(proxy=proxy, api_key=api_key)
bot.inject_ui()
