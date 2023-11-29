pipeline {
    agent any

    stages {
        stage('Fetch Branches with Recent Commits') {
            steps {
                script {
                    def repoSlug = 'insurance-frontend'  // Replace with your Bitbucket repository slug
                    def workspace = 'tjohns-thunder'  // Replace with your Bitbucket workspace

                    def apiUrl = "https://api.github.com/repos/${workspace}/${repoSlug}/branches"
                    def today = new Date()
                    def recentBranches = []

                    // Fetch branches from Bitbucket
                    def branchesResponse = sh(script: "curl -s ${apiUrl}", returnStdout: true).trim()
                    def branches = readJSON(text: branchesResponse).values

                    // Iterate through branches and check commit dates
                    branches.each { branch ->
                        def commitUrl = branch.target.links.commits.href
                        def commitsResponse = sh(script: "curl -s ${commitUrl}", returnStdout: true).trim()
                        def commits = readJSON(text: commitsResponse).values

                        if (commits && commits.size() > 0) {
                            def lastCommitDate = new Date(commits[0].date)
                            def daysDifference = (today - lastCommitDate) / (1000 * 60 * 60 * 24)

                            if (daysDifference <= 90) {
                                recentBranches.add(branch.name)
                            }
                        }
                    }
                    echo "Recent Branches: ${recentBranches}"
                    // Export the list of recent branches
                    //writeFile file: 'recent_branches.txt', text: recentBranches.join('\n')
                }
            }
        }
    }
}
