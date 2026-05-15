# Skill spring-boot-best-practices
### Copiar el siguiente Prompt para crear el skill una vez ejecutado el comando /skill-creator:

```
1. Skill name: ‘spring-boot-best-practices’ 
2. Create new Skill, description: que Genere un proyecto con la estructura de Spring Boot con capas de controllers, services, repository y models.
3. Location for skill folder, crealo dentro del directorio del proyecto ‘.claude/skills‘
4. Specific Instructions:
    - usar java version 25
    - usar spring boot 4.0.6 o superior disponible revisar en documentación de spring boot en spring.io
    - usar configuracion basada en properties: application.properties, en vez de YAML
    - usar packaging jar
    - usar maven como package manager
    - group-id de maven com.andres.course.claude.springboot
    - nombre del proyecto o artifact-id maven mismo que el directorio workspace del proyecto
    - package base de spring boot com.andres.course.claude.springboot.{artifactId}.app
    - revisar e incluir dependencias base, web, validation, spring data jpa, h2, devtools, actuator
    - incluir Maven Wrapper (mvnw) en folder `.mvn` y ejecutable `./mvnw` y `./mvnw.cmd`
    - arquitectura repository/service/controller
    - crear estructura base con packages models, controllers, repositories y services
    - `controller`: expone únicamente puntos finales REST, valida la entrada, delega a servicios y devuelve DTO
    - `service`: contiene lógica de negocios, organiza repositorios y convierte entre entidades y DTO
    - repositorios como interfaces que extienden JpaRepository.
    - para dto usar record en vez de class, excluir campos sensibles como password, created_at, updated_at, create_at, update_at, usar mismo dto request y response
    - Agregue clase Mapper, un mapeador para que el servicio pueda convertir entre DTO y entidades explícitamente. mapper convert `entity -> dto` and `dto -> entity`
    - Para interfaces web de Spring Boot o monolito con spring web use plantillas de Thymeleaf, un diseño compartido y Tailwind CSS. Con layout reutilizable con header, navbar, footer y contenido principal centrado. Use semantica HTML with: `header`, `main`, and `footer`, use diseños base, fragmentos compartidos y la estructura de vistas de Thymeleaf, ejemplo `layouts/base.html`, `fragments/header.html`, `fragments/footer.html`.
    - Cuando necesite iniciar la aplicación localmente con Maven, siempre prefiera `./mvnw, ejemplo -DskipTests spring-boot:run` como comando de ejecución predeterminado.
    - registrar en CLAUDE.md: Available skills y Skill trigger rules, si no existe lo creas
5. Activation Triggers: cuando el usuario pide crear una API básica para `spring boot` o para monolito `spring web`. Tambien cuando el usuario pida crear o agregar o modificar un entity, repository, service o controller de spring.
