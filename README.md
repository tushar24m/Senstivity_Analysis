# Sensitivity_Analysis
The goal of this program is to conduct a sensitivity analysis on a given financial model. This model evaluates the Net Present Value (NPV) of an investment considering various parameters. 
The analysis helps understand how changes in these parameters impact the NPV, allowing for informed decision-making in investment planning.

The below portion of the code imports necessary libraries and defines a function calculate_npv that computes the Net Present Value (NPV) of a project given acquisition cost, annual incoming and outgoing cash flows, salvage value, service life, and interest rate. It discounts future cash flows to present value and returns the NPV.
It also defines parameter ranges for conducting sensitivity analysis. The user is prompted to enter base case values for the project's financial parameters.

            # Import required libraries
            import numpy as np
            import matplotlib.pyplot as plt
            
            # Define the analysis function
            def calculate_npv(acquisition_cost, annual_incoming, annual_outgoing, salvage_value, service_life, interest_rate):
                # Initialize the list of cashflows with the negative of the acquisition cost
                cashflows = [-acquisition_cost]  # Initial investment (negative)
                
                # Loop through each year of the service life
                for year in range(1, service_life + 1):
                    # Calculate the net cashflow for the year
                    net_cashflow = annual_incoming - annual_outgoing
                    
                    # Discount the net cashflow for the current year and add it to the list of cashflows
                    cashflows.append(net_cashflow / (1 + interest_rate)**year)
                
                # Add the salvage value discounted to the last year's cashflow
                cashflows[-1] += salvage_value / (1 + interest_rate)**service_life
                
                # Calculate the NPV by summing all cashflows
                npv = np.sum(cashflows)
                return npv
            
            # Define parameter ranges for sensitivity analysis
            acquisition_cost_range = np.linspace(0, 1000000, 5)
            annual_incoming_range = np.linspace(0, 1000000, 10)
            annual_outgoing_range = np.linspace(0, 1000000, 10)
            salvage_value_range = np.linspace(0, 1000000, 10)
            service_life_range = np.arange(0, 15, 1)
            interest_rate_range = np.linspace(0.01, 0.95, 3)
            
            # Get user input for the base case
            acquisition_cost = float(input("Enter acquisition cost: "))
            annual_incoming = float(input("Enter annual incoming: "))
            annual_outgoing = float(input("Enter annual outgoing: "))
            salvage_value = float(input("Enter salvage value: "))
            service_life = int(input("Enter service life (in years): "))
            interest_rate = float(input("Enter interest rate: "))



In this part, the code performs a sensitivity analysis by calculating NPV values for all possible combinations of parameter values within the defined ranges. 
The nested loops iterate over each parameter's range, and for each combination, it calculates the NPV using the calculate_npv function and stores the results in a multi-dimensional NumPy array called npv_values


            # Perform sensitivity analysis using predefined parameter ranges
            npv_values = np.zeros(
                (len(acquisition_cost_range), len(annual_incoming_range), len(annual_outgoing_range),
                len(salvage_value_range), len(service_life_range), len(interest_rate_range))
            )
            
            # Nested loops to iterate over all combinations of parameter values
            for i, ac in enumerate(acquisition_cost_range):
                for j, ai in enumerate(annual_incoming_range):
                    for k, ao in enumerate(annual_outgoing_range):
                        for l, sv in enumerate(salvage_value_range):
                            for m, sl in enumerate(service_life_range):
                                for n, ir in enumerate(interest_rate_range):
                                    # Calculate NPV for each combination of parameters
                                    npv = calculate_npv(ac, ai, ao, sv, sl, ir)
                                    npv_values[i, j, k, l, m, n] = npv
Here, the code defines a list of percentage changes to test the impact on NPV. It calculates the original NPV using the base case values provided by the user.

            # Define the percentage change values
            percentage_changes = np.array([-20, -10, -5, 0, 5, 10, 20]) / 100
            
            # Calculate original NPV
            original_npv = calculate_npv(acquisition_cost, annual_incoming, annual_outgoing, salvage_value, service_life, interest_rate)

This section calculates the NPV changes caused by varying each individual variable. For each percentage change, it calculates the NPV change for each of the specified variables
('Incoming', 'Outgoing', 'Salvage Value', 'Interest Rate') by adjusting one variable while keeping the others constant.

            # Calculate NPV changes for each variable
            variables = ['Incoming', 'Outgoing', 'Salvage Value', 'Interest Rate']
            npv_changes = []
            
            # Loop over percentage changes and variables to calculate NPV changes
            for i, pct_change in enumerate(percentage_changes):
                npv_changes_per_variable = []
                for var in variables:
                    if var == 'Incoming':
                        # Calculate NPV change when adjusting the incoming cash flows
                        npv_change = calculate_npv(acquisition_cost, annual_incoming * (1 + pct_change), annual_outgoing, salvage_value, service_life, interest_rate) - original_npv
                    elif var == 'Outgoing':
                        # Calculate NPV change when adjusting the outgoing cash flows
                        npv_change = calculate_npv(acquisition_cost, annual_incoming, annual_outgoing * (1 + pct_change), salvage_value, service_life, interest_rate) - original_npv
                    elif var == 'Salvage Value':
                        # Calculate NPV change when adjusting the salvage value
                        npv_change = calculate_npv(acquisition_cost, annual_incoming, annual_outgoing, salvage_value * (1 + pct_change), service_life, interest_rate) - original_npv
                    else:
                        # Calculate NPV change when adjusting the interest rate
                        npv_change = calculate_npv(acquisition_cost, annual_incoming, annual_outgoing, salvage_value, service_life, interest_rate * (1 + pct_change)) - original_npv
                    npv_changes_per_variable.append(npv_change)
                
                npv_changes.append(npv_changes_per_variable)


Finally, the code generates sensitivity analysis plots as follows-
1. The first block of code creates a subplot grid with 2 rows and 2 columns to display individual sensitivity analysis plots for each variable ('Incoming', 'Outgoing', 'Salvage Value', 'Interest Rate').
2. Inside the loop, plt.plot is used to create a line plot. It uses percentage_changes on the x-axis (percentage changes in variables) and the corresponding NPV changes for each variable from the npv_changes list on the y-axis.
3. plt. axhline adds a horizontal dashed line at y=0 to serve as a reference line indicating no change.
4.plt.xlabel and plt. ylabel set the x and y axis labels.
5. plt.title sets the title for each subplot, dynamically changing based on the current variable being analyzed.
6. plt.grid(True) adds a grid to each subplot.
7. plt.tight_layout() ensures proper spacing between subplots to prevent overlapping.
9. plt. show() displays the subplot grid.
10. The second block of code creates a single plot that combines NPV changes for all variables. It loops through the variables and creates a line plot for each variable's NPV changes, using labels for the legend.
11. plt. legend() adds a legend to the plot, indicating which line corresponds to each variable.



                # Plot the Sensitivity graph
                plt.figure(figsize=(12, 8))
                
                # Create subplots for each variable's sensitivity analysis
                for i, var in enumerate(variables):
                    plt.subplot(2, 2, i+1)  # Create a subplot grid with 2 rows and 2 columns
                    plt.plot(percentage_changes * 100, [row[i] for row in npv_changes])  # Plot percentage_changes on x-axis and corresponding NPV changes on y-axis
                    plt.axhline(y=0, color='black', linestyle='--', linewidth=0.8)  # Add a horizontal dashed line at y=0 for reference
                    plt.xlabel("Percentage Change")
                    plt.ylabel("Change in NPV")
                    plt.title(f"Sensitivity Analysis - {var}")  # Set the subplot title
                    plt.grid(True)  # Add a grid to the plot
                
                plt.tight_layout()  # Adjust layout to prevent overlapping
                plt.show()  # Display the subplot grid
                
                # Plot the overall sensitivity analysis
                plt.figure(figsize=(10, 6))
                
                # Create a single plot showing NPV changes for all variables
                for i, var in enumerate(variables):
                    plt.plot(percentage_changes * 100, [row[i] for row in npv_changes], label=var)  # Similar to the previous loop, but with a label for the legend
                
                plt.axhline(y=0, color='black', linestyle='--', linewidth=0.8)  # Add a horizontal dashed line at y=0 for reference
                plt.xlabel("Percentage Change")
                plt.ylabel("Change in NPV")
                plt.title("Sensitivity Analysis - Impact on NPV")  # Set the plot title
                plt.legend()  # Add a legend to the plot
                plt.grid(True)  # Add a grid to the plot
                
                plt.tight_layout()  # Adjust layout
                
The rest of the code is similar to the first subplot grid, adding a horizontal dashed line, setting labels and title, and adding a grid.
Finally, plt.tight_layout() is called again to adjust the layout of the combined plot.
These plots help visualize how changes in each variable impact the project's NPV, aiding in decision-making and understanding the sensitivity of the NPV to different factors.










