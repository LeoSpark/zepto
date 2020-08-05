pipeline {
  agent any
  stages {
    stage('检出') {
      steps {
        checkout([$class: 'GitSCM',
        branches: [[name: env.GIT_BUILD_REF]],
        userRemoteConfigs: [[
          url: env.GIT_REPO_URL,
          credentialsId: env.CREDENTIALS_ID
        ]]])
      }
    }
    stage('安装依赖') {
      steps {
        sh 'yarn'
      }
    }
    stage('单元测试') {
      steps {
        sh 'yarn test:ci'
        junit '*.xml'
      }
    }
    stage('编译') {
      steps {
        sh 'yarn build'
      }
    }
    stage('上传到 COS Bucket') {
      steps {
        sh "coscmd config -a ${env.COS_SECRET_ID} -s ${env.COS_SECRET_KEY} -b ${env.COS_BUCKET_NAME} -r ${env.COS_BUCKET_REGION}"
        sh 'coscmd upload -r ./build/ /'
        echo "上传成功，访问 https://${env.COS_BUCKET_NAME}.cos.${env.COS_BUCKET_REGION}.myqcloud.com/index.html 预览效果"
      }
    }
  }
}