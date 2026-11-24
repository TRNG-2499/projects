# Project Instructions

## Project Overview

You will build an Angular single-page application that consumes data from a
public REST API.

The theme of your application is entirely your choice. Examples include:

- Weather
- Movies
- Recipes
- Books
- Sports
- Finance
- Game statistics
- Travel

Your application's theme should be built around a publicly available API.

Users should be able to browse, search, filter, and view information
retrieved from the external API through a responsive and easy-to-use
interface.

The application must also include a simple authentication system that
allows users to log in and log out. Authentication may be simulated
using browser storage or application state and does not require a
backend server.

Your application should demonstrate modern Angular development practices
while providing a polished user experience.

This project will be completed in small groups (3-4 developers). Team 
members should collaborate on the application's design, architecture, and
implementation while dividing responsibilities across the project. Each
associate should contribute meaningful functionality to the application
and participate in planning, development, testing, and documentation.

## Team Assignments - TBA


Before getting started on the project, let your trainer know your project theme. All project
**themes will be first-come-first-served**, and no two teams should be doing the same project theme.

## Tools and Technologies Used

### Required Technologies

- Angular
- Angular CLI (to generate, serve, build, and test the application)
- TypeScript
- HTML5
- CSS3 (or SCSS)
- Angular Router
- Angular HttpClient
- Angular Forms (Reactive Forms)
- RxJS
- Node.js + npm
- Git
- GitHub

### Required Concepts

- Standalone Components
- Component Composition
- Inputs and Outputs (component communication)
- Signals and Computed State
- Services and Dependency Injection
- Built-in Template Control Flow (`@if`, `@for`, `@switch`)
- Data Binding and Event Binding
- Pipes
- Client-side Routing
- Route Guards
- Forms
- Form Validation
- API Consumption
- Asynchronous Programming (Observables)
- Local Storage or Session Storage
- Responsive Design

## Suggested Architecture

### Components

Responsibilities include:
- Creating reusable, standalone UI elements
- Displaying application data
- Managing user interaction
- Communicating with parent and child components through inputs and outputs
- Keeping components modular and maintainable

### Services

Responsibilities include:
- Making API requests with HttpClient
- Managing communication with external APIs
- Organizing reusable API logic
- Handling and translating request errors
- Being provided through Angular dependency injection

### Routing

Responsibilities include:
- Managing navigation between pages
- Protecting authenticated routes with route guards
- Supporting login and logout navigation
- Handling unknown routes (e.g., a "not found" page)

### State Management

Responsibilities include:
- Managing authentication state in a shared service
- Managing API data
- Tracking loading and error states
- Sharing data between components where appropriate, using signals and/or RxJS

## Instructions

1. **Set Up the Project**\
   Create an Angular application using the Angular CLI, initialize a Git
   repository, and commit changes regularly throughout development.

2. **Plan the Application**\
   Work with your team to choose an application theme, select an
   external REST API, and divide project responsibilities.

3. **Create the User Interface**\
   Build reusable standalone components and multiple pages connected
   through the Angular Router.

4. **Consume an External API**\
   Use Angular services and HttpClient to retrieve, display, search, and
   filter data from a public REST API while handling loading and error
   states.

5. **Implement Authentication**\
   Create a simple login/logout experience using browser storage or
   application state, managed by an authentication service, and protect
   appropriate routes with route guards.

6. **Manage Application State**\
   Use services, dependency injection, and signals (with RxJS where
   appropriate) to organize state and share data between components.

7. **Validate User Input**\
   Use Angular Reactive Forms with built-in and/or custom validators,
   and display meaningful feedback for invalid data and application
   errors.

8. **Test the Application**\
   Write unit tests using the Angular CLI's test tooling to verify
   components, services, route guards, authentication, and API
   communication (using mocked HTTP responses), and verify navigation
   and user interactions.

9. **Document the Project**\
   Create a README describing the application's purpose, technologies,
   architecture, API, setup instructions, features, and known
   limitations.

## User Stories

### Authentication

- US-01: As a user, I want to log into the application so that I can
  access protected features.
- US-02: As a user, I want to log out so that my session ends
  securely.
- US-03: As a user, I want my login state to persist while I continue
  using the application.

### Navigation

- US-04: As a user, I want to navigate between pages without
  refreshing the browser.
- US-05: As a user, I want protected pages to require authentication
  before they can be viewed.

### External API

- US-06: As a user, I want to view information retrieved from an
  external API.
- US-07: As a user, I want the application to indicate when data is
  loading.
- US-08: As a user, I want to receive meaningful feedback if the API
  cannot be reached.

### Search and Filtering

- US-09: As a user, I want to search available data.
- US-10: As a user, I want to filter the displayed results.
- US-11: As a user, I want to view detailed information about an
  individual item.

### User Experience

- US-12: As a user, I want the application to display information in a
  clean and organized layout.
- US-13: As a user, I want forms to validate my input before
  submission.
- US-14: As a user, I want the application to work across different
  screen sizes.
- US-15: As a user, I want meaningful error messages whenever
  something goes wrong.

## Project Requirements Checklist

- Project is built as an Angular application using the Angular CLI,
  modern Angular development practices, and a clean project structure.
- Application uses reusable standalone components, services with
  dependency injection, signals, and client-side routing to organize
  functionality.
- Application integrates with at least one external REST API through
  HttpClient and appropriately handles loading, success, and error
  states.
- Application implements a functional login/logout experience with
  route guards and persistent authentication state.
- Application provides intuitive navigation, responsive layouts, and a
  polished user experience.
- Application uses Reactive Forms to validate user input and provides
  clear feedback for invalid input and application errors.
- Application demonstrates clean, maintainable, and well-documented
  code following TypeScript and Angular best practices.
- Application includes unit tests for key components, services, and
  guards, along with validation of key application functionality.
- Project includes a complete README with setup instructions,
  application architecture, API documentation, and execution steps.
- Project is maintained in a shared Git repository with meaningful
  commit history demonstrating consistent contributions from all team
  members.
