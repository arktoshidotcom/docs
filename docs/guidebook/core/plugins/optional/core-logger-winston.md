---
title: "Logger - Winston"
---

# Logger - Winston

::: tip
You can find the source code of this package at [packages/core-logger-winston](https://github.com/solar-network/solar-core/tree/develop/packages/core-logger-winston).
:::

## Installation

```bash
yarn add @solar-network/core-logger-winston
```

## Alias

`logger`

## Configuration

```ts
import { formatter } from "./formatter";

export const defaults = {
  transports: {
    console: {
      constructor: "Console",
      options: {
        level: process.env.CORE_LOG_LEVEL || "debug",
        format: formatter(true),
        stderrLevels: ["error", "warn"]
      }
    },
    dailyRotate: {
      package: "winston-daily-rotate-file",
      constructor: "DailyRotateFile",
      options: {
        level: process.env.CORE_LOG_LEVEL || "debug",
        format: formatter(false),
        filename:
          process.env.CORE_LOG_FILE ||
          `${process.env.CORE_PATH_LOG}/%DATE%.log`,
        datePattern: "YYYY-MM-DD",
        zippedArchive: true,
        maxSize: "100m",
        maxFiles: "10"
      }
    }
  }
};
```
