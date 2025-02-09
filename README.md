## Hi there 👋

<!--
**aswin2good/aswin2good** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
import requests
import json

# GitHub Personal Access Token
token = "your_github_personal_access_token"

# Set the headers for authentication
headers = {
    "Authorization": f"Bearer {token}",
    "Content-Type": "application/json"
}

# GraphQL query to fetch data from GitHub profile
query = """
{
  viewer {
    login
    name
    bio
    repositories(first: 100) {
      totalCount
      nodes {
        name
        stargazerCount
        issues(states: OPEN) {
          totalCount
        }
        pullRequests(states: OPEN) {
          totalCount
        }
      }
    }
  }
}
"""

# Make the API request
response = requests.post(
    "https://api.github.com/graphql",
    headers=headers,
    json={'query': query}
)

# Check for errors
if response.status_code != 200:
    print(f"Error fetching data: {response.status_code}")
    print(response.text)
    exit()

# Parse the response JSON
data = response.json()

# Extract the relevant insights
viewer = data["data"]["viewer"]
username = viewer["login"]
name = viewer["name"] or "No name"
bio = viewer["bio"] or "No bio available"
total_repositories = viewer["repositories"]["totalCount"]

repo_insights = []

for repo in viewer["repositories"]["nodes"]:
    repo_name = repo["name"]
    stars = repo["stargazerCount"]
    open_issues = repo["issues"]["totalCount"]
    open_pull_requests = repo["pullRequests"]["totalCount"]
    repo_insights.append({
        "name": repo_name,
        "stars": stars,
        "open_issues": open_issues,
        "open_pull_requests": open_pull_requests
    })

# Displaying insights
print(f"GitHub Profile Insights for {username}:")
print(f"Name: {name}")
print(f"Bio: {bio}")
print(f"Total Repositories: {total_repositories}\n")
print("Repository Insights:")

for repo in repo_insights:
    print(f"Repo: {repo['name']}")
    print(f"  Stars: {repo['stars']}")
    print(f"  Open Issues: {repo['open_issues']}")
    print(f"  Open Pull Requests: {repo['open_pull_requests']}")
    print("-" * 30)
