import os
import requests
from telegram import Bot
import logging

# Replace with your actual Telegram bot token and chat ID
TOKEN = 'YOUR_TELEGRAM_BOT_TOKEN'
CHAT_ID = 'YOUR_CHAT_ID'

# Initialize Telegram bot
bot = Bot(token=TOKEN)

# Set up logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    filename='telegram_file_uploader.log',  # Log to a file
    filemode='a'  # Append mode
)

def download_file(url, dest_folder):
    """Download a file from the given URL to the destination folder."""
    try:
        if not os.path.exists(dest_folder):
            os.makedirs(dest_folder)
        
        response = requests.get(url, stream=True, timeout=10)
        response.raise_for_status()  # Raise an HTTPError for bad responses
        
        file_name = os.path.join(dest_folder, os.path.basename(url))
        with open(file_name, 'wb') as file:
            for chunk in response.iter_content(chunk_size=8192):
                file.write(chunk)
        
        logging.info(f"Downloaded {url} to {file_name}")
        return file_name
    
    except requests.exceptions.RequestException as e:
        logging.error(f"Failed to download {url}: {e}")
        return None

def upload_to_telegram(file_path):
    """Upload a file to Telegram."""
    try:
        with open(file_path, 'rb') as file:
            bot.send_document(chat_id=CHAT_ID, document=file)
        
        logging.info(f"Uploaded {file_path} to Telegram")
    
    except Exception as e:
        logging.error(f"Failed to upload {file_path} to Telegram: {e}")

def process_urls(urls_file, destination_folder):
    """Process each URL from the given file and upload files to Telegram."""
    try:
        with open(urls_file, 'r') as f:
            urls = f.readlines()
        
        for url in urls:
            url = url.strip()
            if url:
                downloaded_file = download_file(url, destination_folder)
                if downloaded_file:
                    upload_to_telegram(downloaded_file)
                    print(f"File from {url} uploaded to Telegram successfully!")
                else:
                    print(f"Failed to download {url}. Check logs for details.")
    
    except FileNotFoundError:
        logging.error(f"File {urls_file} not found.")
    except Exception as e:
        logging.error(f"Error processing URLs from {urls_file}: {e}")

if __name__ == '__main__':
    urls_file = 'urls.txt'
    destination_folder = 'downloads'
    
    # Process URLs and upload files to Telegram
    process_urls(urls_file, destination_folder)
