pipeline {
    agent any
    
    parameters {
        credentials(
            credentialType: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl',
            defaultValue: '',
            description: 'Cloudify credentials',
            name: 'cfy_credentials',
            required: true)
        credentials(
            credentialType: 'Secret text',
            defaultValue: '',
            description: 'Kubernetes API key',
            name: 'kubernetes_api_key',
            required: true)
        string(name: 'environment_id', description: 'Environment ID', trim: true)
        string(name: 'kubernetes_master', description: 'Kubernetes master host', trim: true)
    }

    stages {
        stage('Build Kubernetes Environment') {
            cfyKubernetes credentialsId: "{params.cfy_credentials}",
                deploymentId: "${params.environment_id}",
                k8sMaster: "${params.kubernetes_master}",
                apiKeyCredentialsId: "${params.kubernetes_api_key}",
                definitionFile: "resources.yaml",
                echoInputs: true,
                echoEnvData: true,
                envDataLocation: 'env-data.json'
        }
        stage('Run tests') {
            steps {
                input message: 'Proceed?'
            }
        }
        stage('Destroy Kubernetes Environment') {
            steps {
                deleteCloudifyEnv credentialsId: "${params.cfy_credentials}",
                    deleteBlueprintIfLast: false,
                    deploymentId: "${params.environment_id}",
                    ignoreFailure: false
            }
        }
    }
}
