# Node.js CI/CD Demo - Interview Learning Guide

This is a basic Node.js application with CI/CD setup, designed to help you understand Continuous Integration and Continuous Deployment from an interview perspective.

## 📚 What is CI/CD?

### CI (Continuous Integration)
- **Definition**: Automatically building and testing code every time changes are pushed to a repository
- **Purpose**: Catch bugs early, ensure code quality, and prevent integration issues
- **Common Steps**:
  1. Checkout code from repository
  2. Install dependencies
  3. Run linters (code quality checks)
  4. Run tests
  5. Build the application

### CD (Continuous Deployment/Delivery)
- **Definition**: Automatically deploying code to production/staging after successful CI
- **Purpose**: Automate releases, reduce manual errors, enable faster delivery
- **Common Steps**:
  1. All CI steps must pass
  2. Build production-ready artifacts
  3. Deploy to staging/production environment
  4. Run smoke tests
  5. Notify team of deployment status

## 🏗️ Project Structure

```
.
├── server.js              # Main Express server
├── package.json           # Dependencies and scripts
├── tests/                 # Test files
│   └── server.test.js     # API tests
├── .github/
│   └── workflows/
│       └── ci-cd.yml      # GitHub Actions CI/CD pipeline
└── README.md              # This file
```

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ installed
- npm or yarn package manager

### Installation

```bash
# Install dependencies (this will also create package-lock.json)
npm install

# Run the application
npm start

# Run in development mode (with auto-reload)
npm run dev

# Run tests
npm test
```

### API Endpoints

- `GET /` - Welcome message
- `GET /health` - Health check endpoint
- `GET /api/users` - Get all users
- `POST /api/users` - Create a new user (requires `name` and `email` in body)

## 🔄 CI/CD Pipeline Explained

### Pipeline Location
The CI/CD configuration is in `.github/workflows/ci-cd.yml`

### Pipeline Triggers
The pipeline runs automatically when:
- Code is pushed to `main` or `develop` branches
- Pull requests are created/updated for `main` or `develop` branches

### Pipeline Jobs

#### 1. **Test Job** (`test`)
- **Purpose**: Ensure code quality and functionality
- **Runs on**: Multiple Node.js versions (18.x, 20.x) for compatibility testing
- **Steps**:
  1. Checkout code from repository
  2. Setup Node.js environment
  3. Install dependencies (`npm ci` - clean install)
  4. Run linter (code quality checks)
  5. Run tests
  6. Upload test results as artifacts

#### 2. **Build Job** (`build`)
- **Purpose**: Create production-ready artifacts
- **Runs after**: Test job succeeds
- **Steps**:
  1. Checkout code
  2. Setup Node.js
  3. Install dependencies
  4. Build the application
  5. Upload build artifacts

#### 3. **Deploy Job** (`deploy`)
- **Purpose**: Deploy to production/staging
- **Runs after**: Both test and build jobs succeed
- **Runs only on**: Pushes to `main` branch (not on pull requests)
- **Steps**:
  1. Final verification (run tests again)
  2. Deploy to target environment
  3. (Optional) Run smoke tests
  4. (Optional) Notify team

## 🎯 Interview Questions & Answers

### Q1: What is CI/CD?
**Answer**: CI/CD stands for Continuous Integration and Continuous Deployment/Delivery. CI automatically builds and tests code on every commit, while CD automatically deploys code that passes all tests to production environments.

### Q2: What are the benefits of CI/CD?
**Answer**:
- **Early bug detection**: Catch issues before they reach production
- **Faster releases**: Automate repetitive deployment tasks
- **Consistency**: Same process every time, reducing human error
- **Confidence**: Automated tests ensure code quality
- **Collaboration**: Team members can see build/test status

### Q3: What tools are commonly used for CI/CD?
**Answer**:
- **GitHub Actions** (used in this project)
- **Jenkins**
- **GitLab CI/CD**
- **CircleCI**
- **Travis CI**
- **Azure DevOps**
- **AWS CodePipeline**

### Q4: What's the difference between Continuous Delivery and Continuous Deployment?
**Answer**:
- **Continuous Delivery**: Code is automatically tested and prepared for deployment, but requires manual approval to deploy
- **Continuous Deployment**: Code is automatically deployed to production after passing all tests (no manual approval needed)

### Q5: What should be included in a CI pipeline?
**Answer**:
1. Code checkout
2. Dependency installation
3. Linting/Code quality checks
4. Running tests (unit, integration)
5. Building the application
6. Generating test coverage reports
7. Security scanning (optional)

### Q6: What is a pipeline artifact?
**Answer**: Artifacts are files generated during the CI/CD process (like build outputs, test reports, logs) that are saved and can be downloaded or used in later pipeline stages.

### Q7: How do you handle secrets in CI/CD?
**Answer**: 
- Never commit secrets to code
- Use secret management tools (GitHub Secrets, AWS Secrets Manager, etc.)
- Reference secrets as environment variables in the pipeline
- Example: `${{ secrets.API_KEY }}` in GitHub Actions

### Q8: What is a matrix strategy?
**Answer**: Running the same job across multiple configurations (e.g., different Node.js versions, operating systems) to ensure compatibility. In this project, we test on Node.js 18.x and 20.x.

### Q9: What happens if a test fails in CI?
**Answer**: The pipeline stops, the job is marked as failed, and typically:
- The developer is notified
- Deployment is blocked
- The team can review the failure logs
- The issue must be fixed before merging/deploying

### Q10: How do you rollback a deployment?
**Answer**: 
- Keep previous versions/artifacts
- Use deployment strategies (blue-green, canary)
- Have a rollback script/command ready
- Tag releases in version control
- Use infrastructure as code for easy rollback

## 🔧 Customizing the Pipeline

### Adding More Tests
Add test files in the `tests/` directory and they'll automatically run.

### Adding Linting
1. Install ESLint: `npm install --save-dev eslint`
2. Create `.eslintrc.js` configuration
3. The pipeline will run `npm run lint`

### Deploying to Different Platforms

#### Heroku
```yaml
- name: Deploy to Heroku
  uses: akhileshns/heroku-deploy@v3.12.12
  with:
    heroku_api_key: ${{secrets.HEROKU_API_KEY}}
    heroku_app_name: "your-app-name"
```

#### AWS
```yaml
- name: Deploy to AWS
  run: |
    aws s3 sync ./dist s3://your-bucket-name
    aws cloudfront create-invalidation --distribution-id YOUR_ID --paths "/*"
```

#### Docker
```yaml
- name: Build and push Docker image
  run: |
    docker build -t your-image:latest .
    docker push your-image:latest
```

## 📝 Key Concepts for Interviews

1. **Pipeline**: A series of automated steps that run in sequence
2. **Job**: A collection of steps that run on the same runner
3. **Step**: A single task in a job (e.g., "Install dependencies")
4. **Artifact**: Files saved from a job for later use
5. **Secret**: Encrypted variable for sensitive data
6. **Matrix**: Running jobs across multiple configurations
7. **Conditional Execution**: Running jobs/steps only under certain conditions

## 🧪 Testing the CI/CD Pipeline

1. **Generate lock file (recommended for production)**:
   ```bash
   npm install  # This creates package-lock.json
   git add package-lock.json
   git commit -m "Add package-lock.json"
   ```
   > **Note**: The workflow uses `npm install` which works without a lock file. For production, commit `package-lock.json` and update the workflow to use `npm ci` for faster, more reliable installs.

2. **Push code to GitHub**:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin <your-repo-url>
   git push -u origin main
   ```

2. **View pipeline status**:
   - Go to your GitHub repository
   - Click on "Actions" tab
   - See your pipeline running in real-time

3. **Test failure scenario**:
   - Break a test intentionally
   - Push the change
   - See the pipeline fail
   - Fix the test
   - Push again to see it pass

## 🎓 Practice Exercises

1. Add a new API endpoint and write tests for it
2. Add ESLint configuration and see it run in CI
3. Add a build step that creates a Docker image
4. Configure deployment to a free hosting service (Heroku, Railway, etc.)
5. Add a job that runs security scanning
6. Add notifications (email/Slack) when deployment succeeds/fails

## 📚 Additional Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [CI/CD Best Practices](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)

## 📄 License

MIT

