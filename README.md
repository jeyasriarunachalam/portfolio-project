import streamlit as st
import requests

st.title("GitHub Portfolio Analyzer")

url = st.text_input("Enter GitHub Profile URL")

def extract_username(url):
    return url.rstrip("/").split("/")[-1]

if st.button("Analyze"):
    if url:
        username = extract_username(url)
        api_url = f"https://api.github.com/users/{username}/repos"
        response = requests.get(api_url)
        
        if response.status_code == 200:
            repos = response.json()
            
            total_repos = len(repos)
            readme_count = 0
            
            for repo in repos:
                if repo["description"]:
                    readme_count += 1
            
            score = min(100, total_repos * 5 + readme_count * 5)
            
            st.subheader("GitHub Portfolio Score")
            st.write(f"Score: {score}/100")
            
            st.subheader("Suggestions")
            
            if total_repos < 5:
                st.write("- Add more quality projects.")
            if readme_count < total_repos:
                st.write("- Improve documentation and descriptions.")
            if total_repos > 10:
                st.write("- Archive unused repositories.")
        else:
            st.error("Invalid GitHub URL")
