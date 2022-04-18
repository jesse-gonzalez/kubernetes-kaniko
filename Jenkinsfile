podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: maven
        image: maven:3.8.1-jdk-8
        command:
        - sleep
        args:
        - 99d
      - name: kaniko
        image: gcr.io/kaniko-project/executor:latest
        args:
        - "--context=git://github.com/jesse-gonzalez/kubernetes-kaniko"
        - "--destination=ntnxdemo/kaniko-test-image:1.0"
        volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: kaniko-secret
        secret:
            secretName: myregistrykey
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
  node(POD_LABEL) {
    stage('Get a Maven project') {
      git url: 'https://github.com/jesse-gonzalez/kubernetes-kaniko.git', branch: 'main'
      container('maven') {
        stage('Build a Maven project') {
          sh '''
          echo pwd
          '''
        }
      }
    }

    stage('Build Go Image') {
      container('kaniko') {
        stage('Build a Go project') {
          sh '''
            /kaniko/executor --context `pwd` --destination ntnxdemo/hello-kaniko:1.0
          '''
        }
      }
    }

  }
}