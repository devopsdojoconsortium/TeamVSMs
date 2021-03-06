| ![](./readmeAssets/TeamAssistLogo250.png) | TeamAssist is a ready-to-tweak team and program engagement application designed to help transforming organizations connect coaches and other DevOps practitioners with the people they are seeking to help. <br><br>Every enterprise seeking to become a learning organization needs a specialized CRM-to-Lifecycle management application like TeamAssist. |
| --- |:--- |

# Technologies
TeamAssist runs as a very short full stack app with just two layers. CycleJS in the front handles over 95% of the workload, is highly configurable and runs as a lightweight Chrome Single Page Application. The back end is strictly an Event Sourcing engine that records posted events and enables controlled reading by the Front End. Additional layers for API gateways and other data models are easy to include into the suite when needed.
* [CycleJS](https://cycle.js.org/) ([v7.0.0](https://github.com/cyclejs/cyclejs/releases/tag/v7.0.0))
* [EventStore](https://eventstore.org/) (v4.0.2)

# Features
1. Full, yet lightweight CRM tracking to manage the Digital Transformation coaching efforts for the Enterprise.
    - Import existing program/team leads via JSON or CSV. Sharepoint, etc.
    -  Journal notes, next contact, configurable status fields and org tracing.
    - Easily configurable to include any pre-qualification criteria and algorithms to aid in choosing the most valuable program pursuits for transformation.
1. Multiple User levels: SysAdmin, Admin, Coach, Team member, visitor, with connections between team assignments. See more in [personas](docs/Personas.md) page.
    - All menu items can be configured to be accessible to any level
    - Admins set levels of others
    - Coaches assigned to team  (optional)
    - Team members assigned to team (optional)
    - User Identity easily tied to SSO, Jira API, or standard register/login
1. Coaching Schedule in week blocks by team
     - Assign 1-2 coaches to a team for a 6+ week Dojo engagement
     - Schedule Charters, Consults and more alongside coaching assignments to track transformation team availability, making visible to all.
1. VSM tool for any team
     - By team engagement, create 1-n Value Streams in a clean interface, negating the need for Visio and spreadsheets that require followup work.
     - VSM is easily mutable so the team can track progress, keep the value stream visible, and own their own Kaizen.
     - VSM events never go away, so progress can literally be replayed in the UI. Treat each change like a git commit and consume change history as needed.
     - Track actual numbers for lead and process time progress for future reports of success.
1. Active Team coaching notes and formal weekly reporting
    - Coaching journal notes for ongoing - adhoc note taking with markdown
    - Weekly reports designed for Dojo challenges, but valuable for any multi-week engagement.
    - Configurable for additional team maturity progress measures the enterprise needs to track, including DevOps Radar, Agility Health, etc.

Screenshots available on [features2.md](./docs/features.md)

# Contributing to TeamAssist

## Quick Pre-reqs

- Install git
- Install NodeJS (v10.3.0 - v12.8.0)
- Clone this repo
- `$ npm install`

## Use Chrome for development, use and testing.
TeamAssist is currently only tested to work on Chrome, although Edge and newer FireFox appears to be fine.


# Running and Debugging TeamAssist

To serve TeamAssist locally (http://localhost:8080) on your development machine, after `npm install`, just execute:

```
$ npm start
```

This will launch browser (use chrome) and land you on http://localhost:8080/debug.html, as the index runs the minified version that requires `npm run build` (which you can run in your CI pipeline).

Now, any time you update a JavaScript file in the *src* directory on your machine a new build will be created updating the *dist* directory and refreshing your browser reflecting the update.

The terminal window will show you the rebuild, or a parsing error. On success, refresh browser to see changes:

```
4750135 bytes written to dist/app.js (0.75 seconds) at 10:40:32 AM
File change detected /Users/$user/repos/TeamAssist/dist/app.js
```

# Accessing EventStore Data

Install EventStore and run

### Local EventStore Install
You will need to edit local data in testing -- install EventStore locally. Instructions are super clear at [EventStore](https://eventstore.org/docs/getting-started/) where you will be hitting your local DB via http://localhost:2113/ in the initial configuration. The docker method is very simple, even for the uninitiated.

### Dev session management
You will need to have a session for a dev environment to navigate properly. To do this, just go to http://localhost:8080/#/register to create a workable session in EventStore. Clear your ttId cookie to log out.

You will be registed as a visitor, so to update your access level to admin or sysadmin, you just need to disable (rename) the accessLevel and sessValFilter attributes in src/menuRoutes.js below and update your access level:

```
{ label: "Access Level", req: "No blocking yet.", accessLevel: 3, sessValFilter: true, type: "select",
  name: "loginLevel", opts: loginLevels, numIndex: true, title: "App Permission only settable up to YOUR level!" }
```

In some cases, you may need to bypass CORS to get to data. More on that [here](./readmeAssets/readme.md) 

# Application Architecture

TeamAssist seeks to follow the [clean architecture principles](https://www.youtube.com/watch?v=Nsjsiz2A9mg) laid out by Uncle Bob Martin. The code is highly testable and unaware of the IO devices it touches. It has scaled well without tangling so far, and allows decisions to be deferred. The data architecture has been a fascinating pleasure in app development as the use of Event Sourcing has brought the following benefits:

- Events are very easy to fold into current state.
- Notes fields are designated as 'journals', where all prior entry values are naturally accessible for the view.
- The backend of the application is almost entirely a non-factor for the app, reducing overall complexity significantly.
  - Data schema fully configurable in the front end. 
- The power of the browser (away from the DOM) was proven out to be highly perfomant for data processing.
- After initial data calls on browser reload, subsequent calls are only for new events -- a very light payload.
- Events are easy to re-model as needed,unlike state-based data stores

## Empowered by the [CycleJS](https://cycle.js.org) FRP Framework

### Key Essentials of how we leveraged CycleJS 

### Strict separation of concerns with Model-View-Intent (MVI)
* **Model**: Manages ALL state and business logic using mutable objects.
* **View**: Stateless. Simply implements view logic on received state from model.
* **Intent**: Stateless. Simply observes user events and streams a model-friendly object to model observers.

![](./readmeAssets/img_73442unidirectional_mvi_cycle.png)

### Functional

* Composed, functional programming coding style.
* Reusable functions highly favored.

### Reactive

* [RxJS](http://reactivex.io/rxjs/) means we are coding reactively, not imperatively! RxJS is a library for reactive programming using *Observables*, to make it easier to compose asynchronous or callback-based code.

### Hyperscript

* Leverages hyperscript instead of JSX or HTML. For example: 
`h('div.someClass', "content")` 
 instead of 
`<div class="someClass">content</div>`

