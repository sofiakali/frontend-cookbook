PipelineReact {
  slackChannel = '#ci-frontend-cookbook'
  buildCommand = [
    master: 'npm install && npm run build',
    stage: 'npm install && npm run build',
    development: 'npm install && npm run build',
  ]
  baseURL = 'frontend-cookbook'
  buildDir = 'build'
  bucketURL = [
    master: "gs://${baseURL}.ack.ee/",
    development: "gs://${baseURL}-development.ack.ee/",
  ]
  cloudProject = [
    master: " cabtech-qa-208610",
    stage: "cabtech-qa-208610",
    development: "infrastruktura-1307",
  ]
  nodeEnv = '-e NODE_PATH=./app:./config'
  nodeImage = 'node:6'
  excludeDir = '.git'
}
