# ATLAS MCP Server

[![TypeScript](https://img.shields.io/badge/TypeScript-5.3-blue.svg)](https://www.typescriptlang.org/)
[![Model Context Protocol](https://img.shields.io/badge/MCP-1.0.3-green.svg)](https://modelcontextprotocol.io/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Status](https://img.shields.io/badge/Status-Stable-green.svg)]()
[![GitHub](https://img.shields.io/github/stars/cyanheads/atlas-mcp-server?style=social)](https://github.com/cyanheads/atlas-mcp-server)

ATLAS (Adaptive Task & Logic Automation System) is a Model Context Protocol server that provides hierarchical task management capabilities to Large Language Models. This tool provides LLMs with the structure and context needed to manage complex tasks and dependencies.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [Configuration](#configuration)
- [Task Structure](#task-structure)
- [Tools](#tools)
- [Best Practices](#best-practices)
- [Development](#development)
- [Up Next](#up-next)
- [Contributing](#contributing)
- [License](#license)

## Overview

### Model Context Protocol Server

ATLAS implements the Model Context Protocol (MCP), created by Anthropic, which is a standardized communication protocol between LLMs and external systems. The architecture consists of:

- **Clients** (Claude Desktop, IDEs) that maintain server connections
- **Servers** that provide tools and resources to clients
- **LLMs** that interact with servers through client applications

This architecture creates a secure boundary between LLMs and external systems while enabling controlled access to functionality.

### Core Components

ATLAS is built on several robust core components organized into specialized subsystems:

#### Task Management
- **TaskStore**: Path-based task storage system with:
  * Memory-efficient caching (512MB limit)
  * Automatic cache invalidation
  * Path-based hierarchy management
  * Task validation and verification
  * Comprehensive error handling
  * Periodic memory monitoring
  * Cache cleanup on threshold

- **Status Management**:
  * Basic state transitions
  * Status propagation
  * Dependency validation
  * Parent-child status tracking
  * Status-based task filtering
  * Batch status updates
  * Status consistency checks

- **Dependency System**:
  * Path-based dependency tracking
  * Dependency validation
  * Circular dependency prevention
  * Parent-child relationship repair
  * Dependency cleanup on deletion
  * Cross-task dependency management
  * Dependency status tracking

- **Performance Features**:
  * Memory usage monitoring
  * Automatic cache management
  * Database optimization (vacuum)
  * Relationship repair tools
  * Efficient task retrieval
  * Path pattern matching
  * Memory threshold management

- **Error Handling**:
  * Detailed error logging
  * Operation validation
  * Path format verification
  * Dependency checks
  * Clear error messages
  * Recovery procedures

#### System Infrastructure
- **StorageManager**: Provides durable data persistence with SQLite integration
- **SessionManager**: Handles session lifecycle and task list management
- **ConfigManager**: Manages environment-based configuration
- **ValidationSystem**: Ensures data integrity with Zod schema integration

#### Performance & Monitoring
- **RateLimiter**: Controls request rates (100 req/min)
- **HealthMonitor**: Tracks system health with comprehensive metrics
- **MetricsCollector**: Gathers detailed performance statistics
- **RequestTracer**: Traces request flow with debugging capabilities

Through the MCP protocol, ATLAS empowers LLMs to break down complex projects into manageable tasks, track their progress, and maintain dependencies — all within an organized hierarchical structure.

## Features

### Task Organization
- Hierarchical task structures with parent-child relationships
- Dependency management and validation
- Status tracking and automatic propagation
- Bulk operations for efficient task management
- Session-based task isolation

### Content Support
- Markdown documentation with rich formatting
- Code snippets with multi-language syntax highlighting
- JSON data structures with schema validation
- Rich metadata and hierarchical tagging
- Comprehensive task reasoning documentation
- Decision-making history with context preservation
- Cross-reference support between tasks
- Version tracking for content changes

### System Features
- Memory Management
  * 512MB cache limit
  * 60-second memory monitoring
  * Automatic cache cleanup
  * Memory usage statistics
  * Resource cleanup on shutdown
- Health Monitoring
  * Memory usage tracking
  * Basic error monitoring
  * Component health checks
  * Resource utilization tracking
- Error Handling
  * Error categorization
  * Detailed logging
  * Recovery procedures
  * Context preservation
- Maintenance
  * Database optimization
  * Relationship repair
  * Cache management
  * Resource cleanup

### Performance
- Memory-Optimized Operations
  * Efficient task retrieval
  * Automatic cache management
  * Memory usage monitoring
  * Resource optimization
- Database Management
  * SQLite optimization
  * Vacuum operations
  * Relationship repair
  * Path-based indexing
- Resource Management
  * Memory threshold monitoring
  * Cache invalidation
  * Cleanup procedures
  * Connection management

## Installation

### Setup Steps

1. Clone the repository:
```bash
git clone https://github.com/cyanheads/atlas-mcp-server.git
```

2. Navigate to the project directory:
```bash
cd atlas-mcp-server
```

3. Install dependencies:
```bash
npm install
```

4. Build the project:
```bash
npm run build
```

5. Create a storage directory for tasks:
```bash
mkdir -p ~/Documents/atlas-tasks
```

The server is now ready to be configured and used with your MCP client.

## Configuration

ATLAS requires configuration in your MCP client settings:

```json
{
  "mcpServers": {
    "atlas": {
      "command": "node",
      "args": ["/path/to/atlas-mcp-server/build/index.js"],
      "env": {
        "ATLAS_STORAGE_DIR": "/path/to/storage/directory",
        "ATLAS_STORAGE_NAME": "atlas-tasks",
        "ATLAS_MAX_RETRIES": "3",
        "ATLAS_RETRY_DELAY": "1000",
        "ATLAS_BUSY_TIMEOUT": "5000",
        "ATLAS_CHECKPOINT_INTERVAL": "300000",
        "ATLAS_CACHE_SIZE": "2000",
        "ATLAS_MMAP_SIZE": "30000000000",
        "ATLAS_PAGE_SIZE": "4096"
      }
    }
  }
}
```

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| ATLAS_STORAGE_DIR | Directory for task data storage | ./data |
| ATLAS_STORAGE_NAME | Database file name | atlas-tasks |
| ATLAS_MAX_RETRIES | Maximum retry attempts | 3 |
| ATLAS_RETRY_DELAY | Delay between retries (ms) | 1000 |
| ATLAS_BUSY_TIMEOUT | SQLite busy timeout (ms) | 5000 |
| ATLAS_CHECKPOINT_INTERVAL | WAL checkpoint interval (ms) | 300000 |
| ATLAS_CACHE_SIZE | SQLite cache size | 2000 |
| ATLAS_MMAP_SIZE | SQLite mmap size | 30000000000 |
| ATLAS_PAGE_SIZE | SQLite page size | 4096 |

## Task Structure

Tasks support rich content, metadata, and reasoning documentation within a hierarchical structure (maximum 8 levels deep). All task operations are transactional with automatic validation using Zod schemas:

```typescript
{
  "name": "Implementation Task",
  "description": "Implement core functionality",
  "type": "task",
  "notes": [
    "# Requirements\n- Feature A\n- Feature B",
    "interface Feature {\n  name: string;\n  enabled: boolean;\n}"
  ],
  "reasoning": "Modular development approach chosen for reusability. Key considerations:\n- TypeScript support required\n- Features must be independently toggleable\n- Increased complexity balanced against maintainability\n- Backward compatibility required\n- Cross-browser support essential",
  "metadata": {
    "priority": "high",
    "tags": ["core", "implementation"],
    "reasoning": "Additional context about task decisions",
    "toolsUsed": ["npm", "typescript"],
    "resourcesAccessed": ["package.json", "tsconfig.json"],
    "contextUsed": ["project setup", "build configuration"],
    "created": 1703094689310,
    "updated": 1703094734316,
    "projectPath": "server/core",
    "version": 1
  }
}
```

The reasoning field provides a concise explanation of the task's decision-making process and key considerations. The metadata field includes:

- **priority**: Task urgency level (low/medium/high)
- **tags**: Categorization labels
- **reasoning**: Additional LLM context about decisions
- **toolsUsed**: Tools used by LLM for the task
- **resourcesAccessed**: Resources accessed during task
- **contextUsed**: Key context pieces used in decisions
- **created/updated**: Timestamps in milliseconds
- **projectPath**: Task's project context
- **version**: Task revision number

### Task Storage Features

- **Validation**: Zod schema validation for all fields
- **Caching**: Automatic caching with invalidation
- **Indexing**: Full-text search on content and metadata
- **Transactions**: ACID compliance for all operations
- **Performance**: Optimized retrieval with batch support
- **History**: Change tracking and version management

### Example Task List (Without reasoning)

The following example demonstrates a task breakdown for a personal portfolio website project, showcasing the hierarchical structure and metadata capabilities. The task list was generated from the following prompt:
> Create a comprehensive task plan for a modern, responsive personal portfolio website that showcases a web developer's projects, skills, and professional experience, incorporating best practices in UI/UX design, performance optimization, and accessibility. The site should feature an elegant, minimalist design with smooth animations, dark/light mode support, and interactive project demonstrations, while ensuring cross-browser compatibility and optimal load times.

<details>
<summary><b>Portfolio Website Development Task List</b></summary>

```json
{
  "success": true,
  "data": [
    {
      "id": "4ylBnk6B",
      "name": "Project Setup and Architecture",
      "description": "Initial project setup, tooling configuration, and architectural decisions",
      "notes": [
        {
          "type": "markdown",
          "content": "# Setup Considerations\n- Modern build tooling (Vite/Next.js)\n- TypeScript configuration\n- ESLint and Prettier setup\n- Git repository initialization\n- Folder structure organization"
        }
      ],
      "type": "group",
      "status": "pending",
      "dependencies": [],
      "subtasks": [
        "8cz24Z95",
        "GwqwfnSf",
        "gKayn2ha"
      ],
      "metadata": {
        "created": "2024-12-20T18:51:29.310Z",
        "updated": "2024-12-20T18:52:14.316Z",
        "sessionId": "session-001",
        "taskListId": "task-001",
        "tags": [
          "setup",
          "architecture",
          "configuration"
        ],
        "resolvedSubtasks": [
          {
            "id": "8cz24Z95",
            "name": "Initialize Project with Build Tools",
            "description": "Set up the project with Vite and configure essential build tools",
            "notes": [
              {
                "type": "markdown",
                "content": "# Implementation Steps\n1. Initialize new Vite project with React/TypeScript template\n2. Configure project structure\n3. Set up path aliases\n4. Configure build optimization settings\n\n# Technical Considerations\n- Use Vite for fast development and optimized production builds\n- Configure TypeScript for type safety\n- Set up module resolution and aliases"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:51:59.902Z",
              "updated": "2024-12-20T18:51:59.902Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "setup",
                "build-tools",
                "vite"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "4ylBnk6B"
          },
          {
            "id": "GwqwfnSf",
            "name": "Configure Development Tools and Version Control",
            "description": "Set up code quality tools, linting, formatting, and version control",
            "notes": [
              {
                "type": "markdown",
                "content": "# Setup Tasks\n1. Initialize Git repository\n2. Configure ESLint with TypeScript rules\n3. Set up Prettier for code formatting\n4. Create .gitignore file\n5. Configure pre-commit hooks\n\n# Configuration Files\n- `.eslintrc.json`: ESLint configuration\n- `.prettierrc`: Prettier rules\n- `.editorconfig`: Editor settings\n- `husky`: Git hooks\n\n# Best Practices\n- Enforce consistent code style\n- Enable TypeScript strict mode\n- Set up automated code formatting\n- Configure Git hooks for pre-commit linting"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "8cz24Z95"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:52:06.869Z",
              "updated": "2024-12-20T18:52:06.869Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "tooling",
                "git",
                "code-quality"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "4ylBnk6B"
          },
          {
            "id": "gKayn2ha",
            "name": "Install and Configure Core Dependencies",
            "description": "Set up essential libraries and dependencies for the portfolio website",
            "notes": [
              {
                "type": "markdown",
                "content": "# Core Dependencies\n\n## UI Framework and Styling\n- React for UI components\n- TailwindCSS for styling\n- Framer Motion for animations\n- React Icons for iconography\n\n## State Management and Routing\n- React Router for navigation\n- Zustand/Jotai for state management\n\n## Development Dependencies\n- TypeScript for type safety\n- PostCSS for CSS processing\n- Autoprefixer for browser compatibility\n\n## Testing Framework\n- Vitest for unit testing\n- React Testing Library for component testing\n\n# Configuration Steps\n1. Install production dependencies\n2. Install development dependencies\n3. Configure TailwindCSS\n4. Set up PostCSS\n5. Configure testing environment"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "8cz24Z95"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:52:14.315Z",
              "updated": "2024-12-20T18:52:14.315Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "dependencies",
                "libraries",
                "configuration"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "4ylBnk6B"
          }
        ]
      },
      "parentId": "ROOT-session-001"
    },
    {
      "id": "QTmaDu2O",
      "name": "UI/UX Design Implementation",
      "description": "Design system, component architecture, and responsive layout implementation",
      "notes": [
        {
          "type": "markdown",
          "content": "# Design Requirements\n- Minimalist, elegant design system\n- Dark/light mode theming\n- Responsive layouts\n- Smooth animations and transitions\n- Consistent typography and spacing"
        }
      ],
      "type": "group",
      "status": "pending",
      "dependencies": [],
      "subtasks": [
        "lmtqOVNC",
        "AllDgreE"
      ],
      "metadata": {
        "created": "2024-12-20T18:51:34.428Z",
        "updated": "2024-12-20T18:52:31.900Z",
        "sessionId": "session-001",
        "taskListId": "task-001",
        "tags": [
          "design",
          "ui-ux",
          "frontend"
        ],
        "resolvedSubtasks": [
          {
            "id": "lmtqOVNC",
            "name": "Implement Design System and Theme Configuration",
            "description": "Create a comprehensive design system with dark/light mode support",
            "notes": [
              {
                "type": "markdown",
                "content": "# Design System Components\n\n## Color Palette\n- Primary and secondary colors\n- Neutral shades\n- Accent colors\n- Dark/light mode variants\n\n## Typography\n- Font families (heading and body)\n- Font sizes and line heights\n- Font weights\n- Letter spacing\n\n## Spacing System\n- Base spacing unit\n- Spacing scale\n- Layout margins and padding\n\n## Theme Implementation\n1. Create theme configuration file\n2. Implement theme context/provider\n3. Create theme switching mechanism\n4. Set up CSS custom properties\n5. Create utility classes\n\n# Technical Approach\n- Use CSS variables for dynamic theming\n- Implement theme persistence\n- Create theme toggle component\n- Set up system theme detection"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "gKayn2ha"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:52:23.627Z",
              "updated": "2024-12-20T18:52:23.627Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "design-system",
                "theming",
                "css",
                "dark-mode"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "QTmaDu2O"
          },
          {
            "id": "AllDgreE",
            "name": "Create Responsive Layout System and Core Components",
            "description": "Implement responsive layout system and reusable core components",
            "notes": [
              {
                "type": "markdown",
                "content": "# Layout System\n\n## Grid System\n- Implement responsive grid layout\n- Define breakpoints\n- Create container components\n- Set up layout utilities\n\n## Core Components\n1. Navigation\n   - Responsive navbar\n   - Mobile menu\n   - Navigation links\n\n2. Layout Components\n   - Container\n   - Section\n   - Grid\n   - Flex containers\n\n3. UI Components\n   - Buttons (primary, secondary, ghost)\n   - Cards\n   - Links\n   - Icons\n   - Input fields\n   - Loading states\n\n## Animation System\n- Define animation variables\n- Create transition utilities\n- Implement scroll animations\n- Set up interaction animations\n\n# Implementation Guidelines\n- Use CSS Grid and Flexbox\n- Mobile-first approach\n- Implement smooth transitions\n- Ensure consistent spacing\n- Create component documentation"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "lmtqOVNC"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:52:31.899Z",
              "updated": "2024-12-20T18:52:31.899Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "components",
                "layout",
                "responsive",
                "animations"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "QTmaDu2O"
          }
        ]
      },
      "parentId": "ROOT-session-001"
    },
    {
      "id": "rvxqxsd8",
      "name": "Core Features Development",
      "description": "Implementation of main portfolio features and sections",
      "notes": [
        {
          "type": "markdown",
          "content": "# Key Features\n- Project showcase with interactive demos\n- Skills and expertise section\n- Professional experience timeline\n- Contact form and social links\n- Blog/articles section (optional)"
        }
      ],
      "type": "group",
      "status": "pending",
      "dependencies": [],
      "subtasks": [
        "NUD4rYDu",
        "4ItyKXEL",
        "8CR6zCks"
      ],
      "metadata": {
        "created": "2024-12-20T18:51:39.220Z",
        "updated": "2024-12-20T18:53:00.751Z",
        "sessionId": "session-001",
        "taskListId": "task-001",
        "tags": [
          "features",
          "development",
          "core-functionality"
        ],
        "resolvedSubtasks": [
          {
            "id": "NUD4rYDu",
            "name": "Implement Project Showcase Section",
            "description": "Create an interactive project showcase with filtering and detailed project views",
            "notes": [
              {
                "type": "markdown",
                "content": "# Features\n\n## Project Grid/List\n- Responsive project grid layout\n- Project cards with hover effects\n- Image thumbnails with lazy loading\n- Category/tag filtering system\n\n## Project Details\n- Modal/page for detailed project view\n- Project description and technologies\n- Live demo links\n- GitHub repository links\n- Image gallery/carousel\n\n## Interactive Elements\n- Smooth transitions between views\n- Filter animations\n- Image loading states\n- Interactive demos embedding\n\n## Data Structure\n```typescript\ninterface Project {\n  id: string;\n  title: string;\n  description: string;\n  technologies: string[];\n  images: {\n    thumbnail: string;\n    gallery: string[];\n  };\n  links: {\n    demo?: string;\n    github?: string;\n    live?: string;\n  };\n  category: string[];\n  featured: boolean;\n}\n```\n\n# Implementation Steps\n1. Create project data structure\n2. Implement project grid component\n3. Create project card component\n4. Build filtering system\n5. Implement project detail view\n6. Add animations and transitions"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "AllDgreE"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:52:41.144Z",
              "updated": "2024-12-20T18:52:41.144Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "projects",
                "portfolio",
                "interactive"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "rvxqxsd8"
          },
          {
            "id": "4ItyKXEL",
            "name": "Create Skills and Experience Sections",
            "description": "Implement interactive skills showcase and professional experience timeline",
            "notes": [
              {
                "type": "markdown",
                "content": "# Skills Section\n\n## Skill Categories\n- Frontend Development\n- Backend Development\n- DevOps & Tools\n- Soft Skills\n\n## Visual Elements\n- Skill progress indicators\n- Interactive skill cards\n- Category grouping\n- Skill level visualization\n\n# Experience Timeline\n\n## Timeline Features\n- Vertical/horizontal timeline layout\n- Company/role cards\n- Date ranges\n- Key achievements\n- Technologies used\n\n## Interactive Elements\n- Scroll animations\n- Hover effects\n- Expandable details\n- Filter by technology/skill\n\n## Data Structures\n```typescript\ninterface Skill {\n  name: string;\n  category: string;\n  level: number; // 1-5\n  description?: string;\n  icon?: string;\n  yearStarted: number;\n}\n\ninterface Experience {\n  company: string;\n  role: string;\n  startDate: string;\n  endDate?: string;\n  description: string;\n  achievements: string[];\n  technologies: string[];\n  logo?: string;\n}\n```\n\n# Implementation Steps\n1. Create data structures\n2. Implement skills grid/list\n3. Build experience timeline\n4. Add interactive features\n5. Implement responsive layouts\n6. Add animations and transitions"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "AllDgreE"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:52:50.632Z",
              "updated": "2024-12-20T18:52:50.632Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "skills",
                "experience",
                "timeline"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "rvxqxsd8"
          },
          {
            "id": "8CR6zCks",
            "name": "Implement Contact Form and Social Integration",
            "description": "Create an interactive contact form and integrate social media links",
            "notes": [
              {
                "type": "markdown",
                "content": "# Contact Form\n\n## Form Features\n- Input validation\n- Error handling\n- Success feedback\n- Anti-spam measures\n- Loading states\n\n## Form Fields\n```typescript\ninterface ContactForm {\n  name: string;\n  email: string;\n  subject: string;\n  message: string;\n  recaptcha?: string;\n}\n```\n\n## Technical Implementation\n- Form state management\n- Client-side validation\n- Email service integration (e.g., EmailJS, SendGrid)\n- Rate limiting\n- Error boundaries\n\n# Social Integration\n\n## Features\n- Social media links\n- Professional network links\n- GitHub profile integration\n- Social sharing buttons\n\n## Components\n- Social icons grid\n- Animated hover effects\n- Click tracking\n- Dynamic link handling\n\n# Implementation Steps\n1. Create contact form component\n2. Implement form validation\n3. Set up email service\n4. Add social media integration\n5. Implement error handling\n6. Add loading states and animations\n7. Test form submission\n8. Add analytics tracking\n\n# Best Practices\n- Implement proper form accessibility\n- Add proper ARIA labels\n- Ensure keyboard navigation\n- Provide clear feedback\n- Handle all error states\n- Implement proper security measures"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "AllDgreE"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:53:00.751Z",
              "updated": "2024-12-20T18:53:00.751Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "contact",
                "forms",
                "social-media",
                "integration"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "rvxqxsd8"
          }
        ]
      },
      "parentId": "ROOT-session-001"
    },
    {
      "id": "HOjqKU2N",
      "name": "Performance Optimization",
      "description": "Optimization for speed, performance, and user experience",
      "notes": [
        {
          "type": "markdown",
          "content": "# Optimization Areas\n- Image optimization and lazy loading\n- Code splitting and bundling\n- Caching strategies\n- Performance monitoring\n- Core Web Vitals optimization"
        }
      ],
      "type": "group",
      "status": "pending",
      "dependencies": [],
      "subtasks": [
        "FIheWpfk"
      ],
      "metadata": {
        "created": "2024-12-20T18:51:44.218Z",
        "updated": "2024-12-20T18:53:11.631Z",
        "sessionId": "session-001",
        "taskListId": "task-001",
        "tags": [
          "performance",
          "optimization",
          "web-vitals"
        ],
        "resolvedSubtasks": [
          {
            "id": "FIheWpfk",
            "name": "Implement Performance Optimizations",
            "description": "Optimize website performance focusing on Core Web Vitals and loading speed",
            "notes": [
              {
                "type": "markdown",
                "content": "# Core Web Vitals Optimization\n\n## Largest Contentful Paint (LCP)\n- Optimize image loading\n- Implement responsive images\n- Use WebP format with fallbacks\n- Optimize critical rendering path\n- Implement preloading for key resources\n\n## First Input Delay (FID)\n- Optimize JavaScript execution\n- Implement code splitting\n- Defer non-critical JavaScript\n- Minimize main thread work\n- Optimize event handlers\n\n## Cumulative Layout Shift (CLS)\n- Reserve space for dynamic content\n- Optimize font loading\n- Handle image dimensions\n- Manage layout shifts\n\n# Asset Optimization\n\n## Image Optimization\n```typescript\ninterface ImageOptimization {\n  formats: ['webp', 'avif', 'jpg'];\n  sizes: ['thumbnail', 'medium', 'large'];\n  loading: 'lazy' | 'eager';\n  dimensions: {\n    width: number;\n    height: number;\n  };\n}\n```\n\n## Resource Loading\n- Implement resource hints\n- Use dynamic imports\n- Optimize bundle size\n- Configure caching strategies\n\n# Implementation Steps\n1. Set up performance monitoring\n2. Implement image optimization pipeline\n3. Configure code splitting\n4. Optimize resource loading\n5. Implement caching strategy\n6. Set up performance testing\n\n# Metrics to Track\n- Core Web Vitals\n- Time to Interactive (TTI)\n- First Contentful Paint (FCP)\n- Speed Index\n- Total Blocking Time (TBT)"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "4ItyKXEL",
              "8CR6zCks",
              "NUD4rYDu"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:53:11.630Z",
              "updated": "2024-12-20T18:53:11.630Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "performance",
                "optimization",
                "web-vitals"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "HOjqKU2N"
          }
        ]
      },
      "parentId": "ROOT-session-001"
    },
    {
      "id": "qNXLmR1P",
      "name": "Accessibility and Cross-browser Compatibility",
      "description": "Ensuring the site is accessible and works across all modern browsers",
      "notes": [
        {
          "type": "markdown",
          "content": "# Requirements\n- WCAG 2.1 compliance\n- Semantic HTML structure\n- Keyboard navigation\n- Screen reader compatibility\n- Cross-browser testing"
        }
      ],
      "type": "group",
      "status": "pending",
      "dependencies": [],
      "subtasks": [
        "hwVSfypx"
      ],
      "metadata": {
        "created": "2024-12-20T18:51:49.048Z",
        "updated": "2024-12-20T18:53:22.938Z",
        "sessionId": "session-001",
        "taskListId": "task-001",
        "tags": [
          "accessibility",
          "a11y",
          "cross-browser",
          "testing"
        ],
        "resolvedSubtasks": [
          {
            "id": "hwVSfypx",
            "name": "Implement Accessibility and Cross-browser Support",
            "description": "Ensure WCAG compliance and consistent experience across browsers",
            "notes": [
              {
                "type": "markdown",
                "content": "# Accessibility Implementation\n\n## Semantic HTML\n- Use proper heading hierarchy\n- Implement ARIA landmarks\n- Add descriptive alt text\n- Use semantic HTML elements\n\n## Keyboard Navigation\n- Implement focus management\n- Add skip links\n- Ensure logical tab order\n- Style focus indicators\n\n## Screen Reader Support\n- Add ARIA labels\n- Implement live regions\n- Manage focus announcements\n- Test with screen readers\n\n## Color and Contrast\n- Ensure sufficient contrast ratios\n- Provide visible focus states\n- Test color blindness support\n- Implement high contrast mode\n\n# Cross-browser Testing\n\n## Browser Support Matrix\n```typescript\ninterface BrowserSupport {\n  browsers: {\n    chrome: string[];\n    firefox: string[];\n    safari: string[];\n    edge: string[];\n  };\n  features: {\n    name: string;\n    support: 'full' | 'partial' | 'none';\n    fallback?: string;\n  }[];\n}\n```\n\n## Testing Checklist\n- Layout consistency\n- Animation performance\n- Touch interactions\n- Form behavior\n- Media playback\n- Font rendering\n\n# Implementation Steps\n1. Implement semantic HTML structure\n2. Add ARIA attributes and roles\n3. Implement keyboard navigation\n4. Set up cross-browser testing\n5. Add fallbacks for unsupported features\n6. Test with accessibility tools\n\n# Testing Tools\n- WAVE Evaluation Tool\n- Axe DevTools\n- NVDA/VoiceOver\n- Lighthouse\n- BrowserStack\n- Cross-browser testing suite"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "FIheWpfk"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:53:22.937Z",
              "updated": "2024-12-20T18:53:22.937Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "accessibility",
                "a11y",
                "cross-browser",
                "testing"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "qNXLmR1P"
          }
        ]
      },
      "parentId": "ROOT-session-001"
    },
    {
      "id": "GDatxR51",
      "name": "Deployment and Documentation",
      "description": "Site deployment, documentation, and maintenance plan",
      "notes": [
        {
          "type": "markdown",
          "content": "# Deployment Checklist\n- CI/CD pipeline setup\n- Domain configuration\n- SSL certification\n- Documentation\n- Backup strategy"
        }
      ],
      "type": "group",
      "status": "pending",
      "dependencies": [],
      "subtasks": [
        "jJU9mtgM"
      ],
      "metadata": {
        "created": "2024-12-20T18:51:53.339Z",
        "updated": "2024-12-20T18:53:33.815Z",
        "sessionId": "session-001",
        "taskListId": "task-001",
        "tags": [
          "deployment",
          "documentation",
          "devops"
        ],
        "resolvedSubtasks": [
          {
            "id": "jJU9mtgM",
            "name": "Set Up Deployment and Documentation",
            "description": "Configure deployment pipeline and create comprehensive documentation",
            "notes": [
              {
                "type": "markdown",
                "content": "# Deployment Configuration\n\n## CI/CD Pipeline\n- Set up GitHub Actions workflow\n- Configure build process\n- Implement automated testing\n- Set up deployment environments\n- Configure SSL/TLS\n\n## Deployment Environments\n```typescript\ninterface DeploymentConfig {\n  environments: {\n    development: {\n      url: string;\n      variables: Record<string, string>;\n    };\n    staging: {\n      url: string;\n      variables: Record<string, string>;\n    };\n    production: {\n      url: string;\n      variables: Record<string, string>;\n    };\n  };\n  buildConfig: {\n    command: string;\n    output: string;\n    optimization: boolean;\n  };\n}\n```\n\n# Documentation\n\n## Technical Documentation\n- Project setup guide\n- Architecture overview\n- Component documentation\n- API documentation\n- Performance guidelines\n\n## Maintenance Guide\n- Deployment procedures\n- Update processes\n- Backup procedures\n- Monitoring setup\n- Security guidelines\n\n## Content Management\n- Content update guide\n- Image optimization guide\n- SEO guidelines\n- Analytics setup\n\n# Implementation Steps\n1. Set up deployment pipeline\n2. Configure domain and SSL\n3. Set up monitoring tools\n4. Create technical documentation\n5. Write maintenance guides\n6. Set up backup procedures\n\n# Deployment Checklist\n- SSL configuration\n- DNS setup\n- Environment variables\n- Build optimization\n- Cache configuration\n- Security headers\n- Monitoring tools\n- Backup system\n- Analytics integration"
              }
            ],
            "type": "task",
            "status": "pending",
            "dependencies": [
              "hwVSfypx"
            ],
            "subtasks": [],
            "metadata": {
              "created": "2024-12-20T18:53:33.814Z",
              "updated": "2024-12-20T18:53:33.814Z",
              "sessionId": "session-001",
              "taskListId": "task-001",
              "tags": [
                "deployment",
                "documentation",
                "devops",
                "maintenance"
              ],
              "resolvedSubtasks": []
            },
            "parentId": "GDatxR51"
          }
        ]
      },
      "parentId": "ROOT-session-001"
    }
  ],
  "metadata": {
    "timestamp": "2024-12-20T18:53:38.418Z",
    "requestId": "zcIC0XQG",
    "sessionId": "session-001"
  }
}
```

</details>

## Tools

### Task Management

#### create_task
Creates a new task with path-based hierarchy and dependency management. Example:

```typescript
{
  "name": "Implement Authentication",
  "path": "server/auth",
  "description": "Implement secure user authentication system",
  "type": "milestone",
  "notes": [
    "# Authentication System\n- JWT-based auth\n- Password reset flow\n- Session management",
    "interface AuthConfig {\n  tokenExpiry: number;\n  maxAttempts: number;\n}"
  ],
  "reasoning": "Using JWT with Redis for scalable session management. Considered session-based auth but chose JWT for better scalability.",
  "metadata": {
    "priority": "high",
    "tags": ["security", "api"],
    "toolsUsed": ["read_file", "write_to_file"],
    "resourcesAccessed": ["src/auth/*"],
    "contextUsed": ["Security best practices"],
    "created": 1703094689310,
    "updated": 1703094734316,
    "projectPath": "server/auth",
    "version": 1
  }
}
```

Key Features:
- Path-based hierarchy (max 8 levels)
- Rich task metadata and context
- LLM operation tracking
- Dependency management
- Status propagation rules

Features:
- Hierarchical validation with depth limits
- Duplicate name prevention within parent
- Circular dependency detection
- Rich content validation
- Automatic index management
- Transaction-based creation
- Status propagation handling
- Cache-aware operations

#### bulk_create_tasks
Creates multiple tasks efficiently with optimized processing:

```typescript
{
  "parentId": string | null,  // Common parent for batch (null for root)
  "tasks": Array<{
    name: string,            // Task name (required, unique within parent)
    description?: string,    // Optional description
    notes?: Note[],         // Optional rich content notes
    reasoning?: TaskReasoning, // Optional decision documentation
    type?: TaskType,        // Optional task type (default: "task")
    dependencies?: string[], // Optional dependencies
    metadata?: {            // Optional metadata
      context?: string,
      tags?: string[]
    },
    subtasks?: CreateTaskInput[] // Optional nested tasks
  }>
}
```

Features:
- Parallel processing with configurable batch size
- Atomic transaction handling
- Bulk validation optimization
- Efficient index updates
- Automatic rollback on failure
- Status propagation batching
- Memory-efficient processing
- Real-time progress tracking

#### update_task
Updates an existing task with comprehensive validation and impact analysis:

```typescript
{
  "taskId": string,         // Task ID to update
  "updates": {
    "name"?: string,        // New name (unique within parent)
    "description"?: string, // New description
    "notes"?: Note[],      // Updated notes with validation
    "reasoning"?: TaskReasoning, // Updated decision documentation
    "type"?: TaskType,     // New type with validation
    "status"?: TaskStatus, // New status with transition validation
    "dependencies"?: string[], // Updated dependencies with validation
    "metadata"?: {         // Updated metadata
      "context"?: string,
      "tags"?: string[]
    }
  }
}
```

Features:
- Optimistic concurrency control
- State machine-based status transitions
- Intelligent status propagation
- Dependency impact analysis
- Transaction-based updates
- Automatic rollback on failure
- Cache synchronization
- Index maintenance
- History preservation

#### delete_task
Safely removes a task with comprehensive cleanup and impact management:

Features:
- Recursive subtask deletion with validation
- Dependency relationship cleanup
- Status propagation to dependent tasks
- Reference removal from indexes
- Transaction-based deletion
- Automatic rollback on failure
- Cache invalidation
- Impact analysis before deletion
- Dependent task blocking
- In-progress task protection

### Task Retrieval

#### get_task
Retrieves a task by ID with comprehensive context and optimizations:

Features:
- Complete task details with rich content validation
- Status information with transition history
- Dependency data with validation state
- Metadata inheritance and context chain
- Error context with recovery suggestions
- Multi-level cache utilization
- Index-based fast retrieval
- Automatic cache refresh
- Transaction consistency
- Cross-reference resolution

#### get_subtasks
Lists subtasks with hierarchical context and relationship tracking:

Features:
- Direct child task enumeration
- Status aggregation and inheritance
- Dependency relationship validation
- Metadata context propagation
- Hierarchical depth tracking
- Index-optimized retrieval
- Parallel data fetching
- Cache-aware operations
- Batch loading support
- Real-time updates

#### get_task_tree
Retrieves the complete task hierarchy with advanced features:

Features:
- Full task tree with depth limits (max 5)
- Status aggregation and inheritance
- Dependency graph resolution
- Metadata context merging
- Optimized batch loading
- Cache utilization strategy
- Parallel tree construction
- Memory-efficient processing
- Index-based acceleration
- Real-time tree updates

#### get_tasks_by_status
Filters tasks by status with comprehensive querying:

Features:
- Multi-dimensional status filtering
- Parent/child hierarchy support
- Dependency context validation
- Metadata-based filtering
- Session/list scoping
- Index-optimized retrieval
- Parallel query processing
- Cache-aware operations
- Memory-efficient results
- Real-time updates

### Session Management

#### create_session
Creates a new session with comprehensive management features:

```typescript
{
  "name": string,           // Session name (required, unique)
  "metadata": {            // Optional session metadata
    "tags": string[],      // Categorization tags
    "context": string,     // Session context
    "created": string,     // Creation timestamp
    "updated": string,     // Last update timestamp
    "stats": {             // Session statistics
      "taskCount": number,
      "completedTasks": number,
      "activeTaskLists": number
    }
  }
}
```

Features:
- Unique session name validation
- Automatic ID generation
- Transaction-based creation
- Metadata indexing
- Statistics tracking
- Cache initialization
- Cross-session relationship handling
- Cleanup scheduling

#### create_task_list
Creates a task list with advanced organization features:

```typescript
{
  "name": string,           // List name (required, unique in session)
  "description": string,    // List description
  "metadata": {            // Optional metadata
    "tags": string[],      // Categorization tags
    "context": string,     // List context
    "created": string,     // Creation timestamp
    "updated": string,     // Last update timestamp
    "stats": {             // List statistics
      "taskCount": number,
      "completedTasks": number,
      "activeSubtasks": number
    }
  },
  "persistent": boolean    // Cross-session persistence
}
```

Features:
- Session-scoped uniqueness validation
- Automatic ID generation
- Transaction-based creation
- Metadata indexing
- Statistics tracking
- Cache initialization
- Cross-list relationship handling
- Cleanup scheduling

#### switch_session
Switches sessions with state preservation:

Features:
- Session existence validation
- State transition management
- Task context preservation
- Active session updating
- Cache synchronization
- Index updating
- Transaction handling
- Cross-session relationship maintenance
- Statistics updating
- Cleanup scheduling

#### switch_task_list
Switches task lists with context management:

Features:
- List existence validation
- State transition handling
- Context preservation
- Active list updating
- Cache synchronization
- Index updating
- Transaction handling
- Cross-list relationship maintenance
- Statistics updating
- Cleanup scheduling

#### list_sessions
Lists sessions with comprehensive information:

Features:
- Optional archive inclusion
- Rich metadata retrieval
- Statistics aggregation
- Creation/update tracking
- Task list summaries
- Cache utilization
- Parallel processing
- Memory optimization
- Real-time updates
- Filtering capabilities

#### list_task_lists
Lists task lists with detailed context:

Features:
- Optional archive inclusion
- Rich metadata retrieval
- Task statistics
- Status aggregation
- Cache utilization
- Parallel processing
- Memory optimization
- Real-time updates
- Filtering support
- Cross-reference resolution

### Storage Operations

#### Task Storage
- SQLite-based persistence with WAL mode
- ACID-compliant transactions
- Optimistic concurrency control
- Automatic schema migrations
- Connection pooling with retry logic
- Comprehensive error recovery
- Data integrity validation
- Checksum verification
- Efficient batch operations
- Recursive subtask support

#### Session Storage
- Session state persistence
- Task list management
- Active state tracking
- Cross-session relationships
- State transition validation
- Metadata indexing
- Statistics tracking
- Cache initialization
- Cleanup scheduling
- Real-time updates

#### Maintenance Operations
- Automated database analysis
- WAL checkpoint management
- Storage space optimization
- Index maintenance
- Backup rotation
- Resource monitoring
- Performance metrics
- Storage estimation
- Data persistence verification
- Directory structure management

### System Features

#### Rate Limiting
- 100 requests per minute limit
- Automatic request throttling
- Queue management
- Error handling
- Client feedback

#### Health Monitoring
- Component Health Checks
  * Storage system monitoring
  * Rate limiter status
  * Metrics system health
  * Component-level status tracking
  * Real-time health indicators

- Metrics Collection
  * Request count tracking
  * Error rate monitoring (10% threshold)
  * Average duration calculation
  * Per-tool metrics tracking
  * Error frequency analysis

- Rate Limiting
  * 60-second window tracking
  * Request count monitoring
  * Limit enforcement
  * Automatic request throttling
  * Status reporting

- Health Status Reporting
  * Component-level health status
  * Detailed error reporting
  * Timestamp-based tracking
  * Health check aggregation
  * System status overview

#### Performance Metrics
- Request Tracking
  * Success/failure counting
  * Duration measurements
  * Tool-specific metrics
  * Error categorization
  * Hourly statistics

- Tool Analytics
  * Per-tool success rates
  * Average operation duration
  * Error frequency tracking
  * Usage patterns
  * Performance trends

- System Metrics
  * Storage system stats
  * Memory usage tracking
  * Request rate monitoring
  * Component health status
  * Resource utilization

#### Error Handling
ATLAS provides comprehensive error handling:
- Validation errors with context
- Dependency conflict detection
- Task state inconsistencies
- System resource issues
- Transaction failures
- Rate limit violations
- Request timeout handling

## Best Practices

### Task Management
- Create parent tasks before subtasks
- Use task IDs for dependencies
- Provide clear context in metadata
- Use appropriate task types
- Document reasoning and assumptions
- Handle status transitions carefully
- Monitor dependency relationships
- Maintain task hierarchy

### Content Organization
- Use appropriate note types
- Include relevant code samples
- Maintain clear documentation
- Document decision-making process
- Keep metadata current
- Tag tasks appropriately
- Structure hierarchies logically

### Performance Optimization
- Use bulk operations for multiple tasks
- Monitor rate limits
- Handle long-running operations
- Implement proper error handling
- Optimize task retrieval
- Cache frequently accessed data
- Clean up completed tasks

### Error Recovery
- Handle validation errors gracefully
- Resolve dependency conflicts
- Manage status inconsistencies
- Recover from system issues
- Maintain data integrity
- Document error contexts
- Implement retry strategies

## Development

```bash
# Build the project
npm run build

# Watch for changes
npm run watch

# Run MCP inspector
npm run inspector

# Run tests
npm test

# Check types
npm run type-check
```

## Up Next

### Advanced Task Features
- Task templating system
- Task state machine customization
- Advanced task filtering
- Task history tracking
- Task archival system
- Task import/export

### Integration Capabilities
- Webhook support
- Event streaming
- External system integration
- Plugin architecture
- API versioning
- Custom tool support

### Performance Optimizations
- Query plan optimization
- Index usage analysis
- Transaction batching
- Parallel task processing
- Background task cleanup
- Resource usage analytics

### Monitoring Enhancements
- Real-time metrics dashboard
- Performance trend analysis
- Anomaly detection
- Resource usage alerts
- System health reporting
- Operational insights

### Developer Tools
- Task debugging utilities
- Development console
- Performance profiling
- Testing framework
- Documentation generator
- Schema validation tools

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

For bugs and feature requests, please [create an issue](https://github.com/cyanheads/atlas-mcp-server/issues).

## License

Apache License 2.0

---

<div align="center">
Built with the Model Context Protocol
</div>
