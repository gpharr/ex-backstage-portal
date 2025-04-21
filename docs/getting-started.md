# ![App Flow Diagram](assets/growmark.svg)

This repository contains the codebase for the **myGROWMARK Solutions Center UI**, the front-end interface powering a modern and dynamic user experience for the GROWMARK Solutions Center.

---

## 🚀 Quick Start

To get up and running locally:

```bash
# Clone the repository
git clone <this-repo-url>

# Install dependencies
npm install

# Start the development server
npm run serve
```

---

## 🌐 Live Environments

| Environment   | URL                                      | Description                                      |
|---------------|-------------------------------------------|--------------------------------------------------|
| 🟢 **Production** | [mygrowmark.com](https://mygrowmark.com)       | Public-facing environment for end users.         |
| 🔶 **Beta**       | [beta.mygrowmark.com](https://beta.mygrowmark.com) | Staging environment for final testing.           |
| 🔷 **Test**       | [test.mygrowmark.com](https://test.mygrowmark.com) | Isolated QA testing environment.                 |
| ⚙️ **Development** | [dev.mygrowmark.com](https://dev.mygrowmark.com)   | Continuous deployment environment for developers. |

---

## 🎋 Branching Structure

![App Flow Diagram](assets/myGMK%20UI%20Flow.drawio.png)

| Branch                    | Description |
|-------------------------|-------------|
| `integration`        | Serves as code source for Production environment. Should only ever be modified via Pull Request, and all new branches should be built off of this branch and only ever pull from this branch. |
| `development`| Serves as code source for Development environment. Any working branches can freely commit changes to this branch to have them auto-build into the Development environment. Branches should **NEVER** pull code from this branch. |
| `test`         | Serves as code source for Test environment. Code must be manually merged into this branch and built following the same rules as a production release. This branch and the Test environment and meant to house specific features in isolation for more rigorous testing compared to other features. |
| `[feature]`         | This describes locally created branches, built off of integration, which hold code changes for specific features being worked on by developers. Every given feature should have its own unique branch where only the new feature being worked on is changed when compared to integration branch. These branches are free to pull from integration to keep up with production changes, but they should **NEVER** pull from any other branches.  |

### 🛠 Developer Scripts

| Command               | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `npm run create:branch` | Create a new branch based on `integration`. Prompts for STRY # and description. |
| `npm run create:pr`     | Create a pull request with automated naming based on the current branch.     |
| `npm run push:dev`      | Push the current branch to `development`. Triggers CodeBuild for deployment. |

---

## 📦Build and Deployment Pipeline

### Pre-Build Checklist

To minimize issues before attempting a build, follow these steps:

- Ensure no outstanding commits exist using `git fetch`
- Ensure local node_modules is up-to-date with possible changes using `npm install`
- `npm run build:prod` - Build project locally to check for potential errors when building for Production.
- `npm run compile` - Run the TypeScript compiler on the project to check for type errors.

### Deploying Production Release

- If API changes have been made, update `.env` and change `VUE_APP_MYGROWMARK_API_VERSION` to the newest tagged version of the API release

- Create a new UI release using `npm run [patch/minor] "Optional Message"`
  - Patch should be used for small releases with minimal changes, or a hotfix
  - Minor should be used for larger releases, typically during standard CAB release cycles
- Within AWS Console (Prod), navigate to CodeBuild -> Build Projects -> `mygrowmark-ui-prod release` -> Start New Build with Override
  - Select "Single Build"
  - Wait for AWS to connect to Github repo; there's a spinner that displays during this connection period
  - Add generated source version (tag) that was generated from above step
  - Select "Start Build"

#### Accounting for Potential Build Issues

- If issues are being reported post-build, ensure users have refreshed their pages to enable new version
  - Due to the structure of myGROWMARK as a single-page application, changes must be force-retrieved through refresh
- Tags are out-of-sync between branches
  - Use `git fetch --tags`
- Issues caused by any Middleware used in project
  - Run `npm updated` to check if any `@growmark` packages are out of date
  - If outdated, use `npm update @growmark/[package-to-update]`
- If CodeBuild shows a successful build but UI tags don't match with new versions, check if CodeDeploy was successful
  - CodeDeploy is a separate process to actually launch the new version of the site, but when it fails it will not alert the CodeBuild project

---

## 🎨 Design Philosophy

- Maintain a modern, clean, and consistent UI.
- Use smooth transitions and fluid animations where applicable.
- All inputs must clearly reflect their constraints.
- Inputs must give user feedback (e.g., ripples, hover highlights).
- Actions that take time must provide progress feedback (e.g., loading icons).
- Disable interactive UI elements during processing.
- Use skeleton loaders for delayed content rendering.

### Color Palette

Below is the primary color palette used throughout the UI. These colors help maintain a consistent brand identity and visual hierarchy.

| Color        | Preview                                       | Usage |
|--------------------|-----------------------------------------------------|-----------|
| **Primary**        | ![#007BFF](https://placehold.co/15x15/007BFF/007BFF.png) `#007BFF` | Primary myGMK brand color used for interactive elements. |
| **Accent**  | ![#6C757D](https://placehold.co/15x15/6C757D/6C757D.png) `#6C757D` | Secondary color for subtle UI elements and borders. |
| **White**   | ![#FFFFFF](https://placehold.co/15x15/FFFFFF/FFFFFF.png) `#FFFFFF` | Pure white for backgrounds and surfaces. |
| **Black**        | ![#333333](https://placehold.co/15x15/333333/333333.png) `#333333` | Used for primary text and outlines. |
| **Green**        | ![#2ECC71](https://placehold.co/15x15/2ECC71/2ECC71.png) `#2ECC71` |  Indicates success or healthy status items. |
| **Red**          | ![#FF0033](https://placehold.co/15x15/FF0033/007FF0033BFF.png) `#FF0033` | Used for errors, alerts, or failures. |
| **Orange**          | ![#FF851B](https://placehold.co/15x15/FF851B/FF851B.png) `#FF851B` | Represents warnings or clearable items. |
| **Purple**          | ![#9B51E0](https://placehold.co/15x15/9B51E0/9B51E0.png) `#9B51E0` | Used for optional selections or statuses. |

> 💡 These color values are referenced via Vuetify theme configuration in the source code.

---

## 📁 Project Structure

| Path                    | Description |
|-------------------------|-------------|
| `~/composables/`        | Logic modules by feature. Each folder should export via an `index.js`. |
| `~/components/controls/`| Global inputs/displays used across views. |
| `~/components/`         | View-specific components grouped by feature. |
| `~/layouts/`            | Page layout containers. |
| `~/locales/`            | Localized text (also useful for testing). |
| `~/plugins/`            | Vue plugins loaded from `@/plugins/{plugin}.js`. |
| `~/routes/`             | Vue Router definitions. |
| `~/store/`              | Vuex modules for state management. |
| `~/views/`              | Route-bound page components. |
| `.data`, `.mocks/`      | Test/mock data for development and testing. |

---

## 💡 How-To Guides

### VS Code Intellisense Setup

To enable `@` path aliases in VS Code, add this to your `.vscode/settings.json`:

```json
{
  "path-intellisense.mappings": {
    "@": "${workspaceRoot}/src"
  }
}
```

### Add a New Feature Flag

1. Add a new **feature flag** in [`@growmark/ec-lib`](https://github.com/growmark/@growmark/ec-lib).  
2. Assign<sup>1</sup> the feature flag to the appropriate **presentation model**.  
3. Begin implementation and unit testing<sup>2</sup> in parallel.

<sub><sup>1</sup> Backend feature flag assignment can happen in parallel to UI changes.</sub>  
<sub><sup>2</sup> Unit tests can mock the presentation model to test the new feature independently.</sub>