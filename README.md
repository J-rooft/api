# api
[![Build Status](https://travis-ci.com/pruh/api.svg?branch=master)](https://travis-ci.com/pruh/api)
[![Coverage Status](https://coveralls.io/repos/github/pruh/api/badge.svg?branch=master)](https://coveralls.io/github/pruh/api?branch=master)
[![GoDoc](https://godoc.org/github.com/pruh/api?status.svg)](http://godoc.org/github.com/pruh/api)

REST API server written in Go. Supports basic HTTP auth and uses Mongo as a storage.

## Usage

* Rename api.env.template to api.env and add correct its contents, such as Telegram BOT token, basic auth credential, etc.
* Run `docker-compose up -d` to start the server

## api.env

Simple key-value file which will be used by docker to set container environment variables.

* `PORT` mandatory port to use for service.

* `TELEGRAM_BOT_TOKEN` mandatory telegram bot token.

* `TELEGRAM_DEFAULT_CHAT_ID` default telegram chat ID, which will receive messages from the bot. This parameter is optional.

* `API_V1_CREDS` username/password pairs in JSON format of users who are allowed to access API: `{"username1":"password1", "username2":"password2"}`. This parameter is optional.
* `MONGO_INITDB_ROOT_USERNAME` optional mongo username. Will be set only on first start.
* `MONGO_INITDB_ROOT_PASSWORD` optional mongo password. Will be set only on first start.

## List of API methods

### Messages:

API to send messages.

* `/api/v1/telegram/messages/send` POST method which passes message to telegram. It accepts JSON in the following format:

  ```json
  {
      "message": "message to send",
      "chat_id": 1234567890,
      "silent": true
  }
  ```

  where `chat_id` is telegram chat id and `silent` is a flag indicating if message should be sent silently

### Notifications:

API to store and retrive notifications. Notifications that expire will be periodically removed.

The following HTTP methods are supported:

* `/api/v1/notifications/?only_current=true` HTTP GET method to return all notifications.
  
  `/api/v1/notifications/{UUID}` HTTP GET method to return single notification by UUID.

  Methods return notifications in the following format:
  ```json
  {
      "_id": "c146d6f1-8992-4010-85da-80459bb55d10",
      "title": "title",
      "message": "message",
      "start_time": "2020-01-01T00:00:00Z",
      "end_time": "2020-01-01T00:00:00Z",
      "source": "notification source"
  }
  ```

  optional `only_current` query param will filter returned result to include only current (`start_time` <= now <= `end_time`) notifications

* `/api/v1/notifications/` HTTP POST method to save notification.
  Method accepts JSON in the following format:

  ```json
  {
      "title": "title",
      "message": "message",
      "start_time": "2020-01-01T00:00:00Z",
      "end_time": "2020-01-01T00:00:00Z",
      "source": "notification source"
  }
  ```

* `/api/v1/notifications/{UUID}` HTTP DELETE method to delete previously saved notification by UUID.

`message` and `source` are optional

`start_time` and `end_time` should be date time in [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) format

### Providers:

API to store and retrive providers.

The following HTTP methods are supported:

* `/api/v1/providers/` HTTP GET method to return all providers.
  
  `/api/v1/providers/{UUID}` HTTP GET method to return a single provider by UUID.

  Methods return providers in the following format:
  ```json
  {
      "_id": "c146d6f1-8992-4010-85da-80459bb55d10",
      "type": "NJTransit",
      "njtransit": {
        "orig_station_code":"AA",
        "dest_station_code":"AB"
      }
  }
  ```

  based on `type` different optional data will present in json, like `njtransit` in the example above

* `/api/v1/providers/` HTTP POST method to save a provider.
  Method accepts JSON in the following format:

  ```json
  {
      "type": "NJTransit",
      "njtransit": {
        "orig_station_code":"AA",
        "dest_station_code":"AB"
      }
  }
  ```

* `/api/v1/providers/{UUID}` HTTP DELETE method to delete a previously saved provider by UUID.
