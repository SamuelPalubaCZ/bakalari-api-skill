# Bakalari API Skill

A universal Claude skill for the Bakalari API v3, enabling easy integration with the Czech school information system.

## About

This skill provides comprehensive documentation for the **Bakalari API v3**, which is used by the Bakaláři OnLine mobile application to communicate with school servers. It enables developers to build applications that can access student data including grades, timetables, homework, events, messages, and more.

**Important**: This is an unofficial community-maintained documentation. The API is owned by the BAKALÁŘI software company.

## Features

This skill teaches Claude how to:

- Authenticate with the Bakalari API using OAuth2 tokens
- Work with all major API modules (grades, timetable, homework, etc.)
- Handle token refresh and error scenarios
- Generate code in any programming language
- Understand API request/response structures

## Modules Covered

- **Authentication** - OAuth2 flow and token management
- **User** - User profile and permissions
- **Marks** - Grades and evaluations
- **Timetable** - Schedule and lessons
- **Homework** - Assignments and tasks
- **Events** - School events calendar
- **Komens** - Messaging system
- **Absence** - Attendance records
- **Subjects** - Subject catalog
- **Substitutions** - Schedule changes

## Installation

### For Claude Code Users

1. Clone this repository to your local skills directory:
   ```bash
   git clone https://github.com/SamuelPalubaCZ/bakalari-api-skill.git ~/.claude/skills/bakalari-api
   ```

2. The skill will be automatically available when you mention Bakalari integration.

### For Claude Marketplace

Available soon on the Claude Marketplace. Search for "bakalari-api".

## Usage

### Example Prompts

Once installed, you can use prompts like:

- "Create a Python Bakalari API client with authentication"
- "Write JavaScript code to fetch grades from Bakalari"
- "Generate Java code for getting timetable"
- "How do I authenticate with the Bakalari API?"
- "Build a Bakalari app that shows homework"

### How It Works

When you mention Bakalari API integration, Claude will:

1. Access the comprehensive API documentation
2. Understand the authentication flow
3. Generate code in your preferred language
4. Handle error cases and best practices

## Documentation Structure

```
bakalari-api/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── skills/
│   └── bakalari-api/
│       ├── SKILL.md         # Main skill guide
│       └── references/      # Detailed docs
│           ├── authentication.md
│           ├── endpoints.md
│           ├── data-models.md
│           ├── error-handling.md
│           └── modules/     # Per-module docs
├── README.md
└── LICENSE
```

## API Overview

### Base URL Pattern

```
https://<school-domain>:<port>/api/3/<endpoint>
```

### Authentication

The API uses OAuth2-style token authentication:

1. **Initial Login**: Get access_token and refresh_token
2. **API Requests**: Use Bearer token in Authorization header
3. **Token Refresh**: Refresh tokens before expiration (~1 hour)

### Quick Example

```http
POST /api/login
Content-Type: application/x-www-form-urlencoded

client_id=ANDR&grant_type=password&username=<user>&password=<pass>
```

```http
GET /api/3/marks
Authorization: Bearer <access_token>
```

## Language Agnostic

This skill is designed to be universal - it doesn't favor any specific programming language. When you ask for code, Claude will generate it in whatever language you're using:

- Python
- JavaScript/TypeScript
- Java
- C#
- Go
- Ruby
- PHP
- Or any other language

## Contributing

Contributions are welcome! This is a community-maintained project.

### Ways to Contribute

1. **Improve documentation** - Fix errors or add clarity
2. **Add examples** - Add language-specific examples
3. **Report issues** - Found a bug? Let us know
4. **Suggest features** - Have an idea? Share it

### Original Documentation

This skill is based on the [bakalari-api-v3](https://github.com/bakalari-api/bakalari-api-v3) documentation repository.

## License

MIT License - See [LICENSE](LICENSE) file for details.

## Disclaimer

This is an unofficial project. The Bakalari API is owned by BAKALÁŘI software company. Use of this API should comply with the terms of service of the Bakaláři system.

## Support

- **Issues**: Use GitHub Issues
- **Documentation**: See the `references/` directory
- **Original API docs**: [bakalari-api-v3](https://github.com/bakalari-api/bakalari-api-v3)

## Acknowledgments

- The [bakalari-api-v3](https://github.com/bakalari-api/bakalari-api-v3) community for comprehensive API documentation
- All contributors who help maintain this skill
- The BAKALÁŘI software company for providing the API

## See Also

- [bakalari-api-v3 Documentation](https://github.com/bakalari-api/bakalari-api-v3)
- [Official Bakaláři Website](https://www.bakalari.cz/)
- Existing client libraries: `bakapi` (Python), `Baka4J` (Java)
