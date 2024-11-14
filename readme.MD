# Semana 1

Pasar la URL de Notas: https://notepad.pw/share/lVxLGwqbambaWmTjPImy

## Creación de OPENAPI
1. Ir al editor de swagger para crear nuestro contrato bajo el estandar OPENAPI URL: https://editor-next.swagger.io/
2. Crear los contratos del Servicio de Inventario
3. Crear el contrato del Servicio de Ordenes

## Creación del microservicio
1. Ingresar a la url para generar el proyecto URL: https://start.spring.io/
2. Ingresar los datos:
    
    Datos:
    - Project: Maven
    - Language: Java
    - Spring Boot: 3.3.5

    Meta Data:
    | MetaData      | Dato                                  |
    |---------------|---------------------------------------|
    | Group         | com.codearqui.serviceinventory        |
    | Artifact      | service-inventory                     |
    | Name          | Microservices Inventory               |
    | Description   | Inventario relacionado a ordenes      |
    | Package Name  | com.codearqui.serviceinventory        |
    | Packagin      | Jar                                   |
    | Java          | 21                                    |

    Depedencias:
    - Spring Reactive Web
    - Lombok
    - Spring Data R2DBC

    Agregar dependencias adicionales:
    ```bash
    <properties>
		<java.version>21</java.version>
		<openapi.generator.version>7.8.0</openapi.generator.version>
		<org.mapstruct.version>1.6.0</org.mapstruct.version>
		<org.projectlombok.version>1.18.34</org.projectlombok.version>
		<lombok-mapstruct-binding.version>0.2.0</lombok-mapstruct-binding.version>
	</properties>
    ```

    ```bash
    <!-- Para API First / Contract First-->
	<dependency>
		<groupId>io.swagger.core.v3</groupId>
		<artifactId>swagger-annotations</artifactId>
		<version>2.2.22</version>
	</dependency>
	<dependency>
		<groupId>io.swagger.parser.v3</groupId>
		<artifactId>swagger-parser</artifactId>
		<version>2.1.22</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-validation</artifactId>
	</dependency>
	<dependency>
		<groupId>org.openapitools</groupId>
		<artifactId>jackson-databind-nullable</artifactId>
		<version>0.2.6</version>
	</dependency>

    <!--Mapper-->
	<dependency>
		<groupId>org.mapstruct</groupId>
		<artifactId>mapstruct</artifactId>
		<version>${org.mapstruct.version}</version>
	</dependency>
    
    <!--Driver Mysql-->
    <dependency>
		<groupId>dev.miku</groupId>
		<artifactId>r2dbc-mysql</artifactId>
		<version>0.8.2.RELEASE</version>
	</dependency>
    ```

    ```bash
    <plugin>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-maven-plugin</artifactId>
	</plugin>
    <plugin>
		<groupId>org.openapitools</groupId>
		<artifactId>openapi-generator-maven-plugin</artifactId>
		<version>${openapi.generator.version}</version>
		<executions>
			<execution>
				<id>microinvetory-v1-server</id>
				<phase>generate-sources</phase>
				<goals>
					<goal>generate</goal>
				</goals>
				<configuration>
					<inputSpec>${project.basedir}/src/main/resources/contract/microinventory.yaml</inputSpec>
					<generatorName>spring</generatorName>
					<supportingFilesToGenerate>ApiUtil.java</supportingFilesToGenerate>
					<configOptions>
						<library>spring-boot</library>
						<useSpringBoot3>true</useSpringBoot3>
						<reactive>true</reactive>
						<useTags>true</useTags>
						<useResponseEntity>false</useResponseEntity>
						<delegatePattern>true</delegatePattern>
						<serializableModel>true</serializableModel>
						<basePackage>${project.groupId}</basePackage>
						<apiPackage>${project.groupId}.api</apiPackage>
						<modelPackage>${project.groupId}.dto</modelPackage>
					</configOptions>
				</configuration>
			</execution>
		</executions>
	</plugin>
    <plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-compiler-plugin</artifactId>
		<configuration>
			<target>${java.version}</target>
			<annotationProcessorPaths>
				<path>
					<groupId>org.mapstruct</groupId>
					<artifactId>mapstruct-processor</artifactId>
					<version>${org.mapstruct.version}</version>
				</path>
				<path>
					<groupId>org.projectlombok</groupId>
					<artifactId>lombok</artifactId>
					<version>${org.projectlombok.version}</version>
				</path>
				<path>
					<groupId>org.projectlombok</groupId>
					<artifactId>lombok-mapstruct-binding</artifactId>
					<version>${lombok-mapstruct-binding.version}</version>
				</path>
			</annotationProcessorPaths>
		</configuration>
	</plugin>
    ```

3. Verificar el IDE que tenga lombok, caso constrario instalar el pluging Lombok
4. Abrir el proyecto en el IDE.
5. Ejecutar el comando:
    ```java
    // Previamente eliminar el UnitTest generado por defecto
    mvn install
    ```
6. Cambiamos el tipo de archivo del properties a yml
7. Configurar para ejecución por perfil:
    - Add Vm option: -Dspring.profiles.active=local
    - Crear el archivo application-local.yml
8. Antes de ejecutar levantar el docker para la base de datos.
    ```java
    mysql:
        image: mysql:latest
        container_name: mysql
        ports:
        - "3306:3306"
        network_mode: bridge
        environment:
        MYSQL_ROOT_PASSWORD: 123456789
        volumes:
        - ./db-scripts/mysql-init.sql:/docker-entrypoint-initdb.d/mysql-init.sql
    ```
9. A Codificar

10. Curls para postman:

	- Listar inventarios
	```bash
	curl --location --request GET 'localhost:8081/services-inventory/inventories'
	```

	- Guardar inventario
	```bash
	curl --location --request POST 'localhost:8081/services-inventory/inventories' \
	--header 'Content-Type: application/json' \
	--data '{
	"idProduct": "AC-1551",
	"nameProduct": "Lentes",
	"price": 123.5,
	"stock": 50
	}'
	```

