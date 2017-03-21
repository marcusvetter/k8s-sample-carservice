def pipeline

node {
    stage('Load pipeline') {
        pipeline = fileLoader.fromGit('shared-pipelines/carservice-pipeline.groovy',
                'https://github.com/marcusvetter/k8s-sample-pipeline.git')
    }
}

pipeline.execute()