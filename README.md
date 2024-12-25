# Testing and Deployment Code for the Dynamic Route Optimization System

# Import necessary libraries
import requests
import unittest

def fetch_traffic_data(api_key, origin, destination):
    """
    Fetch real-time traffic data using TomTom API.
    """
    url = f"https://api.tomtom.com/traffic/services/4/flowSegmentData/absolute/10/json?key={api_key}&point={origin}" \
          f"&destination={destination}"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()
    else:
        return None

def calculate_emissions(distance_km, fuel_efficiency, emission_factor):
    """
    Calculate emissions for a given route.
    """
    fuel_used = distance_km / fuel_efficiency  # in liters
    emissions = fuel_used * emission_factor   # in kg CO2
    return emissions

class TestRouteOptimizationSystem(unittest.TestCase):
    def setUp(self):
        """Setup mock data and API keys."""
        self.api_key = "test_api_key"
        self.origin = "52.376510,4.905420"
        self.destination = "52.367600,4.904100"
        self.vehicle_details = {
            "fuel_efficiency": 15,  # in km/l
            "emission_factor": 2.4,  # kg CO2 per liter
        }

    def test_fetch_traffic_data(self):
        """Test fetching traffic data with mock API key."""
        traffic_data = fetch_traffic_data(self.api_key, self.origin, self.destination)
        self.assertIsNotNone(traffic_data, "Traffic data fetch failed.")

    def test_calculate_emissions(self):
        """Test emission calculation with sample values."""
        distance_km = 10  # Example distance
        emissions = calculate_emissions(distance_km, 
                                        self.vehicle_details['fuel_efficiency'], 
                                        self.vehicle_details['emission_factor'])
        expected_emissions = (distance_km / 15) * 2.4
        self.assertAlmostEqual(emissions, expected_emissions, places=2, 
                               msg="Emission calculation failed.")

if __name__ == "__main__":
    unittest.main()
