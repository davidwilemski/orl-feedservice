#Open Run Log Feed Service

HTTP service for fetching ORL user and group recent activity that also has a realtime/pubsub component for future event notifications.

## Architecture

### Event Processing
1. Subscribe on relevant channels:

  - `orl.events.user.*.runs`
  - `orl.events.user.*.races`

  Incoming event example:
  ```
  {
    "type": "run", // feed entry type (run, race)
    "id": "531a9c0dd1ec593b5dcbbfe4" // id of run or race
  }
  ```
- Update internal feed state for relevant users (if necessary)

- Publish on relevant feed-related event channels.
  - `orl.events.feeds.users.<:userid>.feed` and
  - `orl.events.groups.<:groupid>.feed`

  Publishes the single run or race object onto the relevant channel. This
  is the same format as would be received in one of the HTTP endpoints inside
  of the feed array.

  Example publish data:
  ```
  {
    "id": "531a9c0dd1ec593b5dcbbfe4",
    "distance": 12,
    "user": 4f4a666dc3b5a00534000000 // or user objec
    "time": 5760,
    "date": "03/07/2014",
    "pace": "8:00 pace",
    "notes": "kensington! got lost...",
    "uri": "/u/david/run/531a9c0dd1ec593b5dcbbfe4"
  }
  ```

### Feed API - (HTTP Endpoints)

#### /api/v1/users/:userid

Input:
```
GET /api/v1/users/4f4a666dc3b5a00534000000
```

Output:
```
{
  "user": "4f4a666dc3b5a00534000000", // user id
  "updated": 1394323747, // unix timestamp
  "feed": [
    {
      "type": "run" // feed entry type (run, race)
      "data": {
        "id": "531a9c0dd1ec593b5dcbbfe4",
        "distance": 12,
        "user": 4f4a666dc3b5a00534000000 // or user objec
        "time": 5760,
        "date": "03/07/2014",
        "pace": "8:00 pace",
        "notes": "kensington! got lost...",
        "uri": "/u/david/run/531a9c0dd1ec593b5dcbbfe4"
      },
    }
  ]

}
```
#### /api/v1/groups/:groupid
Input:
```
GET /api/v1/groups/:id
```

Output:
```
{
  "id": "4f4a666dc3b5a00534000000", // group id
  "updated": 1394323747, // unix timestamp
  "feed": [
    {
      "type": "run" // feed entry type (run, race)
      "data": {
        "id": "531a9c0dd1ec593b5dcbbfe4",
        "distance": 12,
        "user": 4f4a666dc3b5a00534000000 // or user objec
        "time": 5760,
        "date": "03/07/2014",
        "pace": "8:00 pace",
        "notes": "kensington! got lost...",
        "uri": "/u/david/run/531a9c0dd1ec593b5dcbbfe4"
      }
    },
    {
    "type": "run",
    "data": {
      "id": "531b4ae8d1ec593b5dcbbfe9",
      "distance": 5.0,
      "user": "51773d50f69d2046da8bd761",
      "time": 2210,
      "date": "03/08/2014",
      "pace": "7:22 pace",
      "notes": "Easy.",
      "uri": "/u/harrisonclark/run/531b4ae8d1ec593b5dcbbfe9"
    }
  ]

}
```
## Future Possibilities
- Provide RSS/Atom feeds
