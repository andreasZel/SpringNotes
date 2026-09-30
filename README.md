# Lesson 1 Controllers

## Controllers

Controllers are the HTTP layer of a Springboot app, its the layer that `exposes functionality` from a defined interface.

This layer `does not know how the methods work internally`, it only knows the input and output of the methods and can control the 
response status, headers and body.

Thats why we `use the interface` of the service and `not the actual implementation`. The interface is passed in a constructor and saved within the controller.

### Create - Define a Controller

1. We first create a package (if not existing) as the rest with a `.controller` in the end e.g.

	```java
		net.satways.gmp.server.{name}.controller
	```
	
2. We create a `class` inside the package and add the `@RestController` annotation above it followed by the root endpoint that we add with `@RequestMapping("path")` annotation.

> Each of these will use the `service` we define as `final` at the top  of our controller and `initialized in constructor`:
>
>	```java 
> private final AircraftCharacteristicsService service;
>
>public AircraftCharacteristicsController (AircraftCharacteristicsService service) {
>		this.service = service;
>	}
>	```

3. To add the endpoints we have mapping annotations:
   - `@GetMapping`
   - `@PostMapping`
   - `@PatchMapping`
   - `@DeleteMapping`
   
   We can add path or path variable using parentheses `()` next to the annotation
   
   - To add a variable we do `("/{var_name}")` and to get the variable passed we add it to the method params using `@PathVariable` annotation, for example:
   
   	```java
   		@GetMapping("/{id}")
		public ResponseEntity<AircraftCharacteristicsDto> getById(@PathVariable Long id) {
			AircraftCharacteristicsDto dto = service.getById(id);
			if (dto == null) return ResponseEntity.notFound().build();
			return ResponseEntity.ok(dto);
		}
   	```
   	
 	- In a non get method we can get the body using `@RequestBody` annotation:
 	
 	```java
 		@PostMapping
   		public ResponseEntity<AircraftCharacteristicsDto> create(@RequestBody AircraftCharacteristicsDto dto) {
			AircraftCharacteristicsDto created = service.create(dto);
			return ResponseEntity.ok(created);
		}
   	```

# Lesson 2 Services

## Services

Services are the logic or buisness layer of the application. They are immutable so,

Services typically have:

1. two final (and or static) fields, static to belong to the class and final to be only setted once

	- The `repository` (described later)
	- `mapper` (described later)

2. a `constructor` that sets repository and mapper once

To define the `contract` of the service usage, we create an `interface`with the methods that will be used (java 8 allows body in interface methods, but it is the default to just expose the methods)

- Benefits of Interface usage

	> This is what the controller will take as a service from it's constructor and will be implemented by the actual service. We use interface to also have the `ability to implement multiple` unlike abstract methods.

	> `Testing`: We can use `Mockito` to mock a db for testing
	> `Proxies`: We can use `a lightweight JDK dynamic proxy` instead of the Spring @Transactional one
	> `Flexibility`: The interface is the module's public surface; everything in `service/impl/` is `free to change`

- Not Braking Functionality on expansion

	> When adding functionality, due to the necessity of method implementation, we are forced to define the interfaces methods.
	>
	> This is avoided using `default` methods. These methods are available even without a declaration in the class that inherits the interface
	>
	> ```java
	>public interface Vehicle {
    >
    >	String getBrand();
    >
    >	String speedUp();
    >
    >	String slowDown();
    >
    >	default String turnAlarmOn() {
    >    	return "Turning the vehicle alarm on.";
    >	}
    >
    >	default String turnAlarmOff() {
    >    	return "Turning the vehicle alarm off.";
    >	}
	>}
	>
	> // the class implementing it
	>public class Car implements Vehicle {
	>
    >	private String brand;
    >
    >	@Override
    >	public String getBrand() {
    >	    return brand;
    >	}
	>
    >	@Override
    >	public String speedUp() {
    >	    return "The car is speeding up.";
    >	}
    >
    >	@Override
    >	public String slowDown() {
    >	    return "The car is slowing down.";
    >	}
	>}
	>
	> // usage
	>public static void main(String[] args) { 
    >	Vehicle car = new Car("BMW");
    >	System.out.println(car.getBrand());
    >	System.out.println(car.speedUp());
    >	System.out.println(car.slowDown());
    >	System.out.println(car.turnAlarmOn()); // use them withoud declaration
    >	System.out.println(car.turnAlarmOff()); // use them withoud declaration
	>}
	> ```

## DTOs

They are the boundary between the app and the outside world or the API Shape.

It is used to map data to a specific format.

`Lombok` is a tool that is mostly used to `automatically create getters and setters` for the defined DTO. It creates them using different annotations.



