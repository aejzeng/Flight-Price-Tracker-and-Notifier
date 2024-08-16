# Flight-Price-Tracker-and-Notifier

### Objective
Develop a Python-based flight price tracking system that automatically monitors the prices of flights from specified cities, updates a Google Sheet with relevant data, and sends alerts when cheaper flights are found. The project integrates web APIs (Flight Search, Sheety, and Twilio), web scraping, and automation techniques to build a real-world application.

### Step-by-Step Guide

#### Step 1: Set Up and Retrieve Data

1. **Populate Google Sheet with City IATA Codes**:
   - Use the [Flight Search API](https://developers.amadeus.com/) to retrieve the IATA codes for cities listed in your Google Sheet. Since many cities have multiple airports, ensure you're getting the city code, not the individual airport code.
   - Implement Sheety API to read and write data to your Google Sheet, ensuring it contains the necessary IATA codes for each city.
   - Use Sheety to enable `PUT` requests, allowing your code to update the Google Sheet with IATA codes.

2. **Configure the Environment**:
   - Store your API keys, including the Amadeus API key, Sheety credentials, and Twilio credentials in a `.env` file. Use the `python-dotenv` package to load these environment variables into your script.

#### Step 2: Use Sheety to Manage Google Sheet Data

1. **Set Up Sheety API**:
   - Create a new Google Sheet and set up a project in Sheety to interact with this sheet. The Google Sheet will store city names, IATA codes, and the lowest recorded flight prices.

2. **Read Data from Google Sheet**:
   - Use the Sheety API to fetch the data from the Google Sheet and store it in a variable (`sheet_data`). This data will be used to look up flights.

3. **Update Google Sheet with IATA Codes**:
   - Check if the `iataCode` column in `sheet_data` is empty. If it is, call the FlightSearch class to retrieve and update the IATA codes using the Amadeus API.
   - Make a `PUT` request to Sheety to update the Google Sheet with these IATA codes.

#### Step 3: Get City IATA Codes using Amadeus API

1. **Authenticate with Amadeus API**:
   - Create a `FlightSearch` class to manage API requests. Use the `__init__` method to authenticate and retrieve an access token using your Amadeus API credentials.

2. **Retrieve IATA Codes**:
   - Use the Amadeus Travel API to search for the IATA codes of cities listed in the Google Sheet. Update the `sheet_data` dictionary with the retrieved codes.

3. **Error Handling**:
   - Implement error handling to manage situations where the API does not return an IATA code or where there is an issue with the API call (e.g., rate limits).

#### Step 4: Search for Cheap Flights

1. **Search for Flights**:
   - Use the Amadeus Flight Offers Search API to search for the cheapest round-trip, non-stop flights from the origin city (e.g., LON) to the destination cities listed in your Google Sheet.
   - Set the search parameters to find flights departing anytime from tomorrow to six months later for one adult, with prices in GBP.

2. **Parse and Analyze Flight Data**:
   - Parse the JSON response from the API to extract details about the cheapest flight for each destination. Use a tool like JSONPathFinder to identify the relevant data paths.

3. **Handle No Flight Scenarios**:
   - Implement exception handling to manage scenarios where no flights are available. In such cases, create a `FlightData` object with "N/A" values.

#### Step 5: Notify of Cheaper Flights

1. **Compare Prices and Send Notifications**:
   - Compare the retrieved flight prices with the lowest prices recorded in the Google Sheet. If a cheaper flight is found, use the Twilio API to send an SMS or WhatsApp message.
   - The message should include the flight price, departure and arrival airport IATA codes, and the outbound and inbound flight dates.

2. **Set Up Twilio for Notifications**:
   - Add your Twilio credentials (Account SID, Auth Token, Virtual Number, and Verified Numbers) to your `.env` file.
   - Implement the `NotificationManager` class to handle sending notifications when a cheaper flight is detected.

### Prerequisites
- Python 3.x
- `requests`, `sheety`, `twilio`, and `python-dotenv` libraries (install using `pip install requests twilio python-dotenv`)
- API keys for Amadeus, Sheety, and Twilio services

### Notes
- To avoid hitting API rate limits, limit the number of destination cities to 5-10 in your Google Sheet.
- Ensure proper error handling to avoid disruptions due to unresponsive APIs or other unexpected issues.
- Regularly update your `.env` file to keep your credentials secure and avoid hardcoding sensitive information.

By following this guide, you will create a functional flight price tracker that automates the process of monitoring flight prices and notifying you when cheaper options are available. This project integrates multiple APIs and offers practical experience in building real-world applications.
