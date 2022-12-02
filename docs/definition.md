---
title: Fern Definition
---

A **Fern Definition** is a set of YAML files that describe your API. Each file may define:

- **[Services](definition/services.md)**: a set of REST endpoints
- **[Types](definition/types.md)**: data model and objects
- **[Errors](definition/errors.md)**: an error name, status code, and can include properties
- **[Docs](definition/docs.md)**: human-readable descriptions
- **[Imports](definition/imports.md)**: share types and errors across YAML files

## An example of a Fern Definition

[View the example on Github](https://github.com/fern-api/fern-examples/blob/main/fern/api/definition/) or here:

```yml
services:
  http:
    MoviesService:
      auth: false
      base-path: /movies
      endpoints:
        createMovie:
          docs: Add a movie to the database
          method: POST
          path: /create-movie
          request: CreateMovieRequest
          response: MovieId

        getMovie:
          method: GET
          path: /{movieId}
          path-parameters:
            movieId: MovieId
          response: Movie
          errors:
            - NotFoundError
            - UnauthorizedError

types:
  Movie:
    properties:
      title: string
      rating:
        type: double
        docs: The rating scale from one to five stars
      id:
        type: MovieId
        docs: The unique identifier for a movie

  CreateMovieRequest:
    properties:
      title: string
      rating: double

errors:
  NotFoundError:
    http:
      statusCode: 404
    type:
      properties:
        id: MovieId

  UnauthorizedError:
    http:
      statusCode: 401
```
