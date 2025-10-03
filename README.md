# stock-portfolio-tracker
import csv

# --- 1. Hardcoded Dictionary for Stock Prices ---
STOCK_PRICES = {
    "AAPL": 180.50,
    "GOOGL": 145.75,
    "MSFT": 420.00,
    "TSLA": 250.25,
    "AMZN": 175.90,
    # You can add more stocks here
}

def get_user_portfolio():
    """
    Prompts the user to input stock names and quantities for their portfolio.
    Returns a dictionary of user holdings: {"STOCK_NAME": quantity}
    """
    portfolio = {}
    print("\n--- Enter Your Stock Holdings ---")
    print(f"Available stocks: {', '.join(STOCK_PRICES.keys())}")
    print("Enter 'done' when finished.")

    # Loop to continuously accept input until the user types 'done'
    while True:
        stock_name = input("Enter stock symbol (e.g., AAPL): ").upper()
        
        if stock_name == 'DONE':
            break
        
        # Check if the stock is in our hardcoded list
        if stock_name not in STOCK_PRICES:
            print(f"⚠️ Error: Stock '{stock_name}' is not in the predefined price list.")
            continue
            
        try:
            # Basic input/output for quantity
            quantity = int(input(f"Enter quantity for {stock_name}: "))
            if quantity <= 0:
                print("Quantity must be a positive number.")
                continue
            
            # Add to portfolio
            portfolio[stock_name] = quantity
            
        except ValueError:
            print("⚠️ Error: Quantity must be a whole number.")
            
    return portfolio

def calculate_investment(portfolio):
    """
    Calculates the total investment value based on the portfolio and hardcoded prices.
    Returns the total value and a list of detailed holding records.
    """
    total_value = 0.0
    holding_details = [] # List to store data for optional file saving

    # Use basic arithmetic for the calculation
    for symbol, quantity in portfolio.items():
        price = STOCK_PRICES.get(symbol)
        
        if price is not None:
            holding_value = price * quantity
            total_value += holding_value
            
            # Record details for display and file saving
            holding_details.append({
                "Stock": symbol,
                "Quantity": quantity,
                "Price": f"${price:,.2f}",
                "Value": f"${holding_value:,.2f}"
            })
            
    return total_value, holding_details

def save_to_file(total_value, details):
    """
    Saves the portfolio details and total value to a .csv file.
    """
    filename = "portfolio_summary.csv"
    try:
        with open(filename, mode='w', newline='') as file:
            writer = csv.writer(file)
            
            # Write header
            writer.writerow(["Stock", "Quantity", "Price", "Value"])
            
            # Write individual holding details
            for detail in details:
                # Remove dollar signs and format from value strings for clean saving
                writer.writerow([
                    detail["Stock"], 
                    detail["Quantity"], 
                    detail["Price"].strip('$'), 
                    detail["Value"].strip('$')
                ])
                
            # Write total summary (blank rows for separation)
            writer.writerow([]) 
            writer.writerow(["TOTAL INVESTMENT VALUE", "", "", f"${total_value:,.2f}"])
            
        print(f"\n✅ Success: Portfolio details saved to '{filename}'")
    except IOError:
        print(f"\n❌ Error: Could not save data to {filename}.")


def main():
    """Main function to run the portfolio tracker."""
    print("Welcome to the Simple Stock Portfolio Tracker 📈")
    
    # Get the user's portfolio holdings
    user_portfolio = get_user_portfolio()
    
    if not user_portfolio:
        print("\nNo stocks entered. Exiting.")
        return

    # Calculate the total investment value
    total_value, details = calculate_investment(user_portfolio)

    # --- Display Total Investment Value ---
    print("\n==============================================")
    print("        STOCK PORTFOLIO VALUATION")
    print("==============================================")
    
    # Display individual holdings
    for detail in details:
        print(f"{detail['Stock']}: {detail['Quantity']} shares @ {detail['Price']} = {detail['Value']}")
    
    print("----------------------------------------------")
    print(f"TOTAL INVESTMENT VALUE: ${total_value:,.2f}")
    print("==============================================")

    # --- Optional File Handling ---
    save_option = input("\nDo you want to save the results to a CSV file? (y/n): ").lower()
    if save_option == 'y':
        save_to_file(total_value, details)
        
    print("\nTracker finished. Goodbye!")

if __name__ == "__main__":
    main()
