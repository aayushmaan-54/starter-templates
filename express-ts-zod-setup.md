# 🚀 Express + TypeScript Starter Setup


## Installing Dependency ✅
```bash
npm i cookie-parser cors dotenv express helmet morgan
```

```bash
npm i -D @tsconfig/node22 @types/morgan @types/cookie-parser @types/cors @types/express @types/node tsx typescript
```

> **Note**: Replace **node22** in `@tsconfig/node22` with your Node.js version. Check with `node -v`.

---

### package.json
Add following to `package.json`:
```json
"type": "module",
"main": "server.js",
"scripts": {
  "dev": "node --import=tsx --watch --env-file=.env src/server.ts",
  "build": "tsc",
  "start": "node dist/server.js",
  "clean": "rm -rf dist"
},
```

---

## 📁 Folder Structure
```
express-ts/                                
├── src/
│   ├── common/
│   │   ├── config/
│   │   │   ├── app.config.ts
│   │   │   └── http-status.config.ts
│   │   ├── exceptions/
│   │   │   ├── app-errors.ts
│   │   │   └── custom-error.ts
│   │   ├── lib/
│   │   │   └── db.ts
│   │   ├── middlewares/
│   │   │   ├── error-handler.ts
│   │   │   └── not-found-handler.ts
│   │   └── utils/
│   │       ├── api-response.ts
│   │       ├── async-handler.ts
│   │       ├── get-env.ts
│   │       └── send-response.ts
│   ├── features/
│   │   ├── auth/
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.route.ts
│   │   │   └── auth.service.ts
│   │   └── ...               # Other features can go here
│   ├── app.ts
│   └── server.ts
├── .env
├── .env.sample
├── tsconfig.json
├── package.json
└── README.md
```

---

## 🛡️ Configuration Files

### TypeScript Config
`./tsconfig.json`
```typescript
{
  "extends": "@tsconfig/node22/tsconfig.json", // Inherit base settings from official Node.js v22 TS config

  "compilerOptions": {
    "outDir": "./dist", // Compiled TS files directory
    "rootDir": "./src", // TS directory for actual code
    "allowJs": true, // Allows compiling .js files too
    "sourceMap": true, // Generates .map files for debugging
    "module": "ESNext", // Sets the module system (import/export instead of require/module.exports)
    "target": "ES2020", // Compiles your code to be compatible with ES2020 environments
    "moduleResolution": "node", // Follow the same rules as Node.js when trying to resolve modules
    "esModuleInterop": true, // Allow both import/export & require/module.exports
    "strict": true, // Turns on all strict type checking
    "skipLibCheck": true, // Skips type-checking of .d.ts files (like ones in node_modules). Speeds up compilation.
    "forceConsistentCasingInFileNames": true, // Ensures that file imports match actual casing on disk
    "baseUrl": ".", // Sets the base path for module resolution
    "isolatedModules": true, // Makes each file compile independently
    "noEmit": false, // Set to false so TS will output JS.
    "resolveJsonModule": true, // Lets you import .json files 
    "allowSyntheticDefaultImports": true // Allows default imports from modules that don't have a default export
  },

  "include": ["src/**/*"], // Only include files in src/ (and its subfolders) for compilation.
  "exclude": ["node_modules", "dist"] // Don’t compile node_modules or dist (compiled output).
}
```

### App Configuration
`./src/common/config/app.config.ts`
```typescript
import "dotenv/config";
import { getEnv } from "../utils/get-env";


interface Config {
  PORT: number;
  NODE_ENV: string;
  APP_ORIGIN: string[];
  BASE_PATH: string;
  JWT: {
    ACCESS_TOKEN_SECRET: string;
    ACCESS_TOKEN_EXPIRES_IN: string;
    REFRESH_TOKEN_SECRET: string;
    REFRESH_TOKEN_EXPIRES_IN: string;
  },
}


const allowedOrigins =
  getEnv("NODE_ENV", "development") === 'development'
    ? ['http://localhost:3000', 'http://localhost:3001']
    : ['https://your-project-name.vercel.app'];
    
  
const config: Config = {
  PORT: Number(getEnv("PORT", "5555")) || 6969 || 7777 || 4321 || 5000,
  NODE_ENV: getEnv("NODE_ENV", "development"),
  APP_ORIGIN: allowedOrigins,
  BASE_PATH: "/api/v1",
  JWT: {
    ACCESS_TOKEN_SECRET: getEnv("JWT_ACCESS_TOKEN_SECRET"),
    ACCESS_TOKEN_EXPIRES_IN: getEnv("JWT_ACCESS_TOKEN_EXPIRES_IN"),
    REFRESH_TOKEN_SECRET: getEnv("JWT_REFRESH_TOKEN_SECRET"),
    REFRESH_TOKEN_EXPIRES_IN: getEnv("JWT_REFRESH_TOKEN_EXPIRES_IN"),
  },
};


export default config;
```

### HTTP Status Configuration
`./src/common/config/http-status.config.ts`
```typescript
const httpConfig = () => ({
  // 2xx: Success
  OK: 200,                       // Request succeeded
  CREATED: 201,                  // Resource successfully created
  ACCEPTED: 202,                 // Request accepted but not completed
  NO_CONTENT: 204,               // Success, but no content returned

  // 3xx: Redirection
  MOVED_PERMANENTLY: 301,      // Resource permanently moved
  FOUND: 302,                  // Resource temporarily found at different URL
  NOT_MODIFIED: 304,           // Resource not modified since last request

  // 4xx: Client Errors
  BAD_REQUEST: 400,            // Invalid client request
  UNAUTHORIZED: 401,           // Authentication required or failed
  FORBIDDEN: 403,              // Authenticated, but not allowed
  NOT_FOUND: 404,              // Resource not found
  METHOD_NOT_ALLOWED: 405,     // HTTP method not supported
  CONFLICT: 409,               // Request conflict (e.g., duplicate entry)
  GONE: 410,                   // Resource no longer available
  UNSUPPORTED_MEDIA_TYPE: 415, // Content-type not supported
  UNPROCESSABLE_ENTITY: 422,   // Semantic errors in request
  TOO_MANY_REQUESTS: 429,      // Rate limiting

  // 5xx: Server Errors
  INTERNAL_SERVER_ERROR: 500,  // Server error
  NOT_IMPLEMENTED: 501,        // Feature not implemented
  BAD_GATEWAY: 502,            // Invalid response from upstream server
  SERVICE_UNAVAILABLE: 503,    // Server unavailable (maintenance or overload)
  GATEWAY_TIMEOUT: 504         // Upstream server failed to respond in time
});


export const HTTPSTATUS = httpConfig();
```

---

## 📚 Core Utilities

### Environment Utilities
`./src/common/utils/get-env.ts`
```typescript
export const getEnv = (
  key: string, 
  defaultValue?: string
): string => {
  const value = process.env[key];

  if (value !== undefined) return value;
  if (defaultValue !== undefined) return defaultValue;

  throw new Error(`Environment variable "${key}" is not set and no default value provided.`);
};
```

### API Response
`./src/common/utils/api-response.ts`
```typescript
class ApiResponse<T = any> {
  statusCode: number;
  data: T | null;
  message: string;
  success: boolean;

  constructor(
    statusCode: number = 200,
    data?: T,
    message: string = "Success"
  ) {
    this.statusCode = statusCode;
    this.data = data ?? null;
    this.message = message;
    this.success = statusCode < 400;
  }
}


export { ApiResponse };
```

### Send API Response
`./src/common/utils/send-response.ts`
```typescript
import { Response } from "express";
import { ApiResponse } from "../utils/api-response";


export const sendResponse = <T>(
  res: Response,
  statusCode: number = 200,
  data?: T,
  message: string = "Success"
): void => {
  res.status(statusCode).json(
    new ApiResponse(statusCode, data, message)
  );
};
```

### Async Handler
`./src/common/utils/async-handler.ts`
```typescript
import {
  Request,
  Response,
  NextFunction,
  RequestHandler
} from 'express';


type AsyncControllerType = (
  req: Request,
  res: Response,
  next: NextFunction
) => Promise<any>;



export const asyncHandler = (controller: AsyncControllerType): RequestHandler =>
  async (req, res, next) => {
    try {
      await controller(req, res, next);
    } catch (error) {
      next(error);
    }
  };
```

---

## 🛠️ Error Handling
### Custom Error Class
`./src/common/exceptions/custom-error.ts`
```typescript
class CustomError extends Error {
  statusCode: number;
  data: null;
  success: false;
  errors: string[];
  
  constructor(
    statusCode: number,
    message: string = "Oops! An unexpected error occurred. Try again.",
    errors: string[] = [],
    stack: string = ""
  ) {
    super(message); // Calls the constructor of the parent class(Error)

    this.name = this.constructor.name; // Sets the .name of the error to the name of the class (CustomError)
    this.statusCode = statusCode;
    this.data = null; // When error occur data is set to undefined which can cause problem in frontend so to fix it overwrite it to null to fix it
    this.success = false;
    this.errors = errors;

    if (stack) {
      this.stack = stack;
    } else {
      Error.captureStackTrace(this, this.constructor); // Start the stack trace from the point where the error is thrown
    } 

    Object.setPrototypeOf(this, new.target.prototype); // When extending class in JS it have weird issue of prototype chain break to fix this we use this so it behave like actual instance of Error class
  }
}


export { CustomError };
```


### App Errors
`./src/common/exceptions/app-errors.ts`
```typescript
import { HTTPSTATUS } from "src/common/config/http-status.config";
import { CustomError } from "./custom-error";



export class NotFoundException extends CustomError {
  constructor(
    message = "Resource not found",
    errors: string[] = [],
    stack?: string
  ) {
    super(
      HTTPSTATUS.NOT_FOUND, 
      message, 
      errors, 
      stack
    );
  }
}


export class BadRequestException extends CustomError {
  constructor(
    message = "Bad request",
    errors: string[] = [],
    stack?: string
  ) {
    super(
      HTTPSTATUS.BAD_REQUEST, 
      message, 
      errors, 
      stack
    );
  }
}


export class UnauthorizedException extends CustomError {
  constructor(
    message = "Unauthorized access",
    errors: string[] = [],
    stack?: string
  ) {
    super(
      HTTPSTATUS.UNAUTHORIZED, 
      message, 
      errors, 
      stack
    );
  }
}


export class InternalServerException extends CustomError {
  constructor(
    message = "Internal server error",
    errors: string[] = [],
    stack?: string
  ) {
    super(
      HTTPSTATUS.INTERNAL_SERVER_ERROR, 
      message, 
      errors, 
      stack
    );
  }
}


export class HttpException extends CustomError {
  constructor(
    statusCode: number,
    message = "HTTP error occurred",
    errors: string[] = [],
    stack?: string
  ) {
    super(
      statusCode, 
      message, 
      errors, 
      stack
    );
  }
}
```

---

## 🔌 Middleware
### Error Handler
`./src/common/middlewares/error-handler.ts`
```typescript
import { ErrorRequestHandler, Response } from "express";
import { CustomError } from "../exceptions/custom-error";
import { ApiResponse } from "../utils/api-response";
import { HTTPSTATUS } from "src/common/config/http-status.config";
import { z } from "zod";



const formatZodError = (res: Response, error: z.ZodError) => {
  const errors = error?.issues?.map((err) => ({
    field: err.path.join("."),
    message: err.message
  }));

  return res.status(HTTPSTATUS.BAD_REQUEST).json({
    success: false,
    message: "Validation failed",
    errors
  });
};


export const errorHandler: ErrorRequestHandler = (
  error,
  req,
  res,
  _next
): void => {
  console.error(`Error occurred on PATH: ${req.path}`, error);

  if (error instanceof SyntaxError) {
    res.status(HTTPSTATUS.BAD_REQUEST).json(
      new ApiResponse(
        HTTPSTATUS.BAD_REQUEST,
        null,
        "Invalid JSON format, please check your request body"
      )
    );
    return;
  }


  if(error instanceof z.ZodError) {
    formatZodError(res, error);
    return;
  }


  if (error instanceof CustomError) {
    res.status(error.statusCode).json({
      statusCode: error.statusCode,
      message: error.message,
      success: false,
      data: null,
      errors: error.errors
    });
    return;
  }

  
  res.status(HTTPSTATUS.INTERNAL_SERVER_ERROR).json(
    new ApiResponse(
      HTTPSTATUS.INTERNAL_SERVER_ERROR,
      null,
      error?.message || "Internal Server Error"
    )
  );
  return;
};
```

### Not Found Handler
`./src/common/middlewares/not-found-handler.ts`
```typescript
import { Request, Response } from "express";
import { ApiResponse } from "../utils/api-response";
import { HTTPSTATUS } from "../config/http-status.config";


export const notFoundHandler = (
  req: Request, 
  res: Response
): void => {
  res.status(HTTPSTATUS.NOT_FOUND).json(
    new ApiResponse(
      HTTPSTATUS.NOT_FOUND, 
      null, 
      `Route ${req.originalUrl} not found`
    )
  );
};
```

---

## 📝 Application Setup

### Express App Configuration
`./src/app.ts`
```typescript
import express from 'express';
import cookieParser from 'cookie-parser';
import cors from 'cors';
import config from './common/config/app.config';
import helmet from 'helmet';
import morgan from "morgan";
import { errorHandler } from './common/middlewares/error-handler';
import { HTTPSTATUS } from './common/config/http-status.config';
import { asyncHandler } from './common/utils/async-handler';
import { notFoundHandler } from './common/middlewares/not-found-handler';



const app = express();

app.use(express.json({ limit: '16kb' }));
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(helmet());
app.use(cors({
  origin: config.APP_ORIGIN,
  credentials: true
}));
if (config.NODE_ENV === 'development') {
  app.use(morgan('dev'));
}



app.get('/health-check', asyncHandler(async(_req, res) => {
  res.status(HTTPSTATUS.OK).json({ message: 'Hello, World! 🌏' });
}));


app.use(notFoundHandler);
app.use(errorHandler);



export default app;
```

### Server Entry Point
`./src/server.ts`
```typescript
import app from './app';
import config from './common/config/app.config';


app.listen(config.PORT, () => {
  console.log(`⚙️  Server listening on http://localhost:${config.PORT}`);
});
```
