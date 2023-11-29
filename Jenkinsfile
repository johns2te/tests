pipeline {
    agent any

    stages {
        stage('Fetch Branches with Recent Commits') {
            steps {
                withCredentials([string(credentialsId: 'github-token', variable: 'ACCESS_TOKEN')]) {
                    script {
                        def repoOwner = 'tjohns-thunder' // Replace with your GitHub repository owner/organization
                        def repoName = 'insurance-frontend' // Replace with your GitHub repository name
                        def apiUrl = "https://api.github.com/repos/${repoOwner}/${repoName}/branches"

                        def today = new Date()
                        def recentBranches = []

                        // Fetch branches from GitHub using the ACCESS_TOKEN
                        def branchesResponse = sh(script: "curl -s -H 'Authorization: token ${ACCESS_TOKEN}' ${apiUrl}", returnStdout: true).trim()
                        def branches = readJSON(text: branchesResponse)

                        branches.each { branch ->
                            def commitUrl = branch.commit.url
                            def commitResponse = sh(script: "curl -s -H 'Authorization: token ${ACCESS_TOKEN}' ${commitUrl}", returnStdout: true).trim()
                            def lastCommitDateStr = readJSON(text: commitResponse).commit.committer.date

                            // Handling date parsing
                            def lastCommitDate = new Date.parse("yyyy-MM-dd'T'HH:mm:ss'Z'", lastCommitDateStr)

                            def daysDifference = (today.time - lastCommitDate.time) / (1000 * 60 * 60 * 24)

                            if (daysDifference <= 90) {
                                recentBranches.add(branch.name)
                            }
                        }


                        // Echo the list of recent branches to Jenkins console
                        echo "Recent Branches: ${recentBranches}"

                        // Export the list of recent branches
                        //writeFile file: 'recent_branches.txt', text: recentBranches.join('\n')
                    }
                }
            }
        }
    }
}
