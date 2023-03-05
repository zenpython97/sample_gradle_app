// week5 example uses Jenkin's "scripted" syntax, as opposed to its "declarative" syntax
// see: https://www.jenkins.io/doc/book/pipeline/syntax/#scripted-pipeline
// Defines a Kubernetes pod template that can be used to create nodes.
podTemplate(containers: [
    containerTemplate(
    name: 'gradle', image: 'gradle:6.3-jdk14', command: 'sleep', args: '30d'
    ),
    ]) {
        node(POD_LABEL) {
            stage('Run pipeline against a gradle project') {
                // "container" Selects a container of the agent pod so that all shell steps are
                // executed in that container.
                container('gradle') {
                    stage('Build a gradle project') {
                    // from the git plugin
                    // https://www.jenkins.io/doc/pipeline/steps/git/
                    git branch: 'main', url: 'https://github.com/zenpython97/sample_gradle_app.git' 
                    sh '''
                    chmod +x gradlew
                    ./gradlew test
                    '''
                    }
                    stage("Code coverage") {
                        try {
                            sh '''
                            pwd
                            ./gradlew jacocoTestCoverageVerification
                            ./gradlew jacocoTestReport
                            '''
                        } catch (Exception E) {
                            echo 'Failure detected'
                        }
                        // from the HTML publisher plugin
                        // https://www.jenkins.io/doc/pipeline/steps/htmlpublisher/
                        publishHTML (target: [
                            reportDir: 'Chapter08/sample1/build/reports/tests/test',
                            reportFiles: 'index.html',
                            reportName: "JaCoCo Report"
                        ])
                    }
                }
            }
        }
    }