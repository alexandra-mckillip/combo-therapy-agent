"""
fetch_trials.py

Description:
Helper function to fetch clinical trial data from
ClinicalTrials.gov for a specified drug.

Author: Alexandra McKillip
"""
import requests

def fetch_trials(drug_name, page_size=50, page_token=None):
    """
        Fetch clinical trials from ClinicalTrials.gov API.

        Parameters:
        drug_name : str
            The search term to query the database (e.g., a drug name).

        page_size : int, optional
            Number of trials to return per API request (default is 50).

        page_token : str, optional
            Token for pagination, if continuing from a previous request.

        Returns:
            JSON response from the API containing trial information.

    """
    # check that search term is valid
    if not drug_name or not drug_name.strip():
        raise ValueError("drug_name must be a non-empty string")

    # ClinicalTrials.gov API endpoint
    url = "https://clinicaltrials.gov/api/v2/studies"

    # API query parameters
    params = {
        "query.term": drug_name, # ClinicalTrials.gov API is case-insensitive
        "pageSize": page_size
    }

    if page_token:
        params["pageToken"] = page_token

    # send an HTTP GET request (timeout prevents hanging)
    response = requests.get(url, params=params, timeout=10)

    # raise error if request failed
    response.raise_for_status()

    return response.json()
