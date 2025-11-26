# multi-docker

A small repository with notes and helpers for running multi-container Docker setups and deploying them to AWS Elastic Beanstalk. The repo contains documentation and scripts aimed at making local development, packaging, and AWS deployment smoother.

## Quick overview

- Local development: run multi-container applications with Docker Compose or individual Dockerfiles.
- AWS deployment: prepare a multi-container Docker environment for Elastic Beanstalk and use the included guide to create and manage environments.
- Documentation: see docs/ for focused walkthroughs, tips and an AWS cheat sheet.

## Prerequisites

- Docker and docker-compose (for local development)
- AWS account and credentials configured (for AWS/EB work)
- AWS CLI and Elastic Beanstalk CLI (recommended for deploying and managing EB environments)

## Quick start

1. Clone the repository:
   git clone https://github.com/vvb-1997/multi-docker.git
2. For local testing, from the project root:
   - docker-compose up --build
   - or build and run individual Dockerfiles as needed
3. To prepare and deploy to Elastic Beanstalk, follow the steps in [Create Beanstalk Env](docs/create_beanstalk_env.md) — it covers creating an EB application, configuring a multi-container platform, and deploying your dockerrun/Docker Compose configuration.

## Documentation

- [Create Beanstalk Env](docs/create_beanstalk_env.md) 
  A step-by-step guide for creating and configuring an AWS Elastic Beanstalk environment suitable for multi-container Docker applications. Includes environment configuration tips, how to prepare your Docker assets for EB, and common troubleshooting steps.

- [AWS cheatsheet](docs/aws_cheat_sheet.md)
  Handy AWS CLI and EB CLI commands, tips for credential/configuration management, and quick references for common tasks (creating roles, setting region/profile, viewing logs, and rolling back deployments).

## Repository layout (high level)

- docs/ — markdown guides and notes (including the two linked documents above)  
- (Other top-level files/directories) — service directories, Dockerfiles, and compose files for local and production builds (project structure may vary by project)

## Contributing

If you'd like to add or improve documentation, guides, or scripts:
- Open an issue or pull request.
- Keep changes small and focused (e.g., one guide or script per PR).
- Add examples and commands that you tested locally.

## License

Include your preferred license or note here (e.g., MIT). If none provided, add one before wide reuse.
