# NestJS

## What is NestJS
- framework for Node.js server-side applications
- built with full support for TypeScript
- combines Object-Oriented, Functional and Reactive Programming
- Makes use of Express by default as a HTTP server
- Solves the main problem of architecture on Node.js server side libs. 
    - provides an out of the box app architecture that allows to create scalable, loosely coupled, easy to maintain and highly testable apps
- ships with a cli that allows to scaffold a project and generate schematics.

### Installing the cli

`npm install -g @nestjs/cli`
or
`yarn global add @nestjs/cli `

### Create new project

`nest new <project-name>`

### NestJS Modules
- singletons
- each app has at least one, the root.
- effective way of organizing components by related capabilities (e.g: feature)
- good practice: have a folder per module that contain the module's components
- defined by annotating a class with the `@Module` decorator.
    - this decorated provides the metadata that Nest uses to organize the app strucuture
    - `@Module` decorator properties:
        - providers: array of providers to be available within the module via dependency injection
        - controllers: array of controllers to be instantiated within the module
        - exports: array of providers to export to other modules
        - imports: list of modules required by this module
    example:
    ```typescript
        @Module({
            providers: [AService],
            controllers: [AController],
            imports:[Module1, Module2, Module3],
            exports: [AService]
        })
        export class myModule {}
    ```

#### Generate module schematic
`nest g module <module_name>`

### NestJS Controllers
- responsible for handling incoming requests and returning responses to the client.
- bound to a specific path 
- contain handlers for each endpoint and request method
- can use dependency injection to consume providers within the same module
- defined by annotating a class with the `@Controller` decorator.
    - accepts a string that is the path to be handled by the controller
    -  example:
    ```typescript
        @Controller('/path')
        export class myController {}
    ```

#### Handler
- methods within the controller class, decorated with decorators like `@Get`, `@Post` ...
  -  example:
    ```typescript
        @Controller('/path')
        export class myController {
            @Get()
            getSomething(){
                return;
            }

             @Post()
            createSomething(){
                return;
            }
        }
    ```

*Receives HTTP request* -> *Request is routed to the controller, handler is called with arguments* -> *Handler handles the request* -> *Handler returns response value*

#### Generate controller
`nest g controller <controller_path> --no-spec`


### NestJS Providers
- Can be injected, using dependeny injection, into constructors if decorated as an `@Injectable`
- Can be plain value, class, factory ...
- Must be provided to a module to be usable
- Can be exported from a module and made available to other modules

#### Service
- Main source of business logic
- Defined as providers. Not all providers are services.
- When wrapped with `@Injectable()` and provided to modules, they act as a Singleton.

```typescript
        @Controller('/path')
        export class myController {
            // NestJS will find the service with this name and that is decorated with @Injectable and inject it here for us 
            constructor(private serviceName: AService){}

            @Get()
            getSomething(){
                return;
            }
        }
```

 #### Generate service
`nest g service <service_name> --no-spec`


### Requests
We can decorate POST request function arguments with `@Body()` to extract the entire body of the request, or `@Body("key")` to get a specific parameter

example:
```typescript
 @Post()
  create(
    @Body('title') title: string,
    @Body('description') description: string,
  ) {
    return;
  }
```
or

```typescript
 @Post()
  create(
    @Body() requestBody
  ) {
    return;
  }
```

We can decorate GET function arguments with `@Param("param")` to extract a get param

example:
```typescript
 @GET()
  get(
    @Param('id') id: string,
  ) {
    return;
  }
```

- By adding a "/:param" to a request it allows you to add that param as an URL param to your request
    - for a @GET("/:id") it would expect that a GET request would be performed for the /controllerName/:id being the :id the URL param to be received


### Data Transfer Objects (DTO)
- object that defines how the data will be sent over the network
- can be defined as classes or interfaces
    - recommended to **use classes**, as they are part of JavaScript and will be preserved post-compilation, unlike interfaces that are a part of TypeScript.
        - also, NestJS cannot refer to interfaces in run-time
    
example:
```typescript
export class CreateTaskDto {
  title: string;
  description: string;
}
```