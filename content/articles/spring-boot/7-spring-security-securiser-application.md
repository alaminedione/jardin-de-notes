---
title: Spring Security  , Sécuriser Votre Application Spring Boot
tags:
  - spring-boot
  - security
  - authentification
  - autorisation
  - jwt
draft: false
---

# Spring Security : Sécuriser Votre Application Spring Boot

## Introduction

La sécurité est une préoccupation majeure dans le développement de toute application, qu'il s'agisse d'une application web, d'une API REST ou d'un microservice. Protéger les données sensibles, contrôler l'accès aux ressources et prévenir les menaces courantes comme les injections SQL ou les attaques CSRF est essentiel. Négliger la sécurité peut avoir des conséquences désastreuses.

C'est là que Spring Security intervient. C'est un framework puissant et hautement configurable qui fournit des services d'authentification (qui êtes-vous ?) et d'autorisation (qu'êtes-vous autorisé à faire ?) pour les applications Spring. Il s'intègre parfaitement avec Spring Boot, offrant une solution de sécurité robuste pour vos applications Java.

**Ce que Spring Security apporte à votre application :**

* **Authentification :** Vérifier l'identité d'un utilisateur ou d'un système. Spring Security supporte une large gamme de mécanismes d'authentification (formulaires de login, HTTP Basic, OAuth2, JWT, etc.).
* **Autorisation :** Déterminer si un utilisateur authentifié a le droit d'accéder à une ressource ou d'exécuter une action particulière. L'autorisation peut être basée sur les rôles, les permissions ou des expressions plus complexes.
* **Protection contre les menaces courantes :** Fournit une protection intégrée contre des vulnérabilités web courantes comme CSRF (Cross-Site Request Forgery), XSS (Cross-Site Scripting), attaque par fixation de session, etc.
* **Intégration profonde :** S'intègre avec d'autres projets Spring (MVC, Data, etc.) et l'écosystème Java (LDAP, bases de données, etc.).
* **Hautement configurable :** Bien que puissant, Spring Security est également très flexible et peut être adapté à presque tous les besoins en matière de sécurité.

Dans cet article, nous allons découvrir les bases de Spring Security dans une application Spring Boot et voir comment mettre en place l'authentification et l'autorisation.

## Contenu Principal

### Configuration De Base

Pour commencer avec Spring Security, vous devez ajouter la dépendance appropriée à votre projet Spring Boot.

#### Dépendances Maven/Gradle

Ajoutez la dépendance `spring-boot-starter-security` à votre fichier `pom.xml` (Maven) ou `build.gradle` (Gradle).

Avec Maven :

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Avec Gradle :

```yaml
implementation 'org.springframework.boot:spring-boot-starter-security'
```

Une fois cette dépendance ajoutée, Spring Boot configure automatiquement Spring Security avec des valeurs par défaut. Par défaut, toutes les requêtes HTTP nécessitent une authentification, et un formulaire de login basique est généré. Un utilisateur par défaut avec un mot de passe généré aléatoirement au démarrage est également créé (le mot de passe est affiché dans les logs de l'application).

#### SecurityFilterChain Et WebSecurityConfigurerAdapter

Dans les versions récentes de Spring Security (5.x et suivantes), la configuration de la sécurité se fait généralement en définissant un ou plusieurs beans de type `SecurityFilterChain`. Ce bean est responsable de la chaîne de filtres de sécurité que les requêtes HTTP traverseront.

Avant Spring Security 6, on utilisait souvent la classe `WebSecurityConfigurerAdapter`. Cependant, cette classe est maintenant dépréciée. La nouvelle approche utilise des classes de configuration basées sur des composants et des beans `SecurityFilterChain`.

Voici un exemple de configuration de base utilisant la nouvelle approche, désactivant la sécurité par défaut pour l'instant afin de montrer une configuration minimale :

```java
package com.example.demo.security;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authz -> authz
                .anyRequest().permitAll() // Permettre toutes les requêtes sans authentification
            );
        return http.build();
    }
}
```

Dans cet exemple :
* `@Configuration` indique que cette classe contient la configuration Spring.
* `@Bean` marque la méthode `filterChain` comme produisant un bean géré par Spring.
* `SecurityFilterChain` est le bean qui configure la sécurité.
* `HttpSecurity` permet de configurer la sécurité web pour des requêtes HTTP spécifiques.
* `authorizeHttpRequests` permet de configurer l'autorisation basée sur les requêtes HTTP.
* `anyRequest().permitAll()` configure toutes les requêtes pour qu'elles soient autorisées sans authentification. (Ceci **désactive** la sécurité par défaut de Spring Boot et n'est PAS recommandé en production, mais utile pour démarrer).

### Authentication

L'authentification est le processus de vérification de l'identité. Qui êtes-vous ?

#### Authentification En Mémoire

Pour des exemples simples ou des tests, vous pouvez configurer des utilisateurs directement en mémoire.

```java
package com.example.demo.security;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class InMemorySecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authz -> authz
                .anyRequest().authenticated() // Toutes les requêtes nécessitent une authentification
            )
            .httpBasic(); // Utiliser l'authentification HTTP Basic

        return http.build();
    }

    @Bean
    public UserDetailsService userDetailsService(PasswordEncoder passwordEncoder) {
        UserDetails user = User.builder()
            .username("user")
            .password(passwordEncoder.encode("password")) // Encode le mot de passe
            .roles("USER") // Assigne le rôle USER
            .build();
        UserDetails admin = User.builder()
            .username("admin")
            .password(passwordEncoder.encode("admin")) // Encode le mot de passe
            .roles("ADMIN", "USER") // Assigne les rôles ADMIN et USER
            .build();
        return new InMemoryUserDetailsManager(user, admin);
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        // Recommandé pour l'encodage des mots de passe
        return new BCryptPasswordEncoder();
    }
}
```

Dans cet exemple :
* `anyRequest().authenticated()` : Indique que toute requête nécessite un utilisateur authentifié.
* `httpBasic()` : Configure l'authentification HTTP Basic.
* `UserDetailsService` : Interface clé pour charger les informations de l'utilisateur (nom d'utilisateur, mot de passe, rôles). Ici, on utilise une implémentation `InMemoryUserDetailsManager`.
* `UserDetails` : Représente un utilisateur (avec nom d'utilisateur, mot de passe, autorités/rôles).
* `PasswordEncoder` : Indispensable pour encoder les mots de passe avant de les stocker ou de les comparer. `BCryptPasswordEncoder` est une implémentation recommandée. **Ne stockez jamais de mots de passe en clair !**

#### Authentification Avec Base De Données (UserDetailsService)

Dans la plupart des applications réelles, les utilisateurs sont stockés dans une base de données. Vous devez implémenter l'interface `UserDetailsService` pour dire à Spring Security comment charger les détails de l'utilisateur depuis votre source de données (par exemple, via un Repository JPA).

```java
package com.example.demo.security;

import com.example.demo.model.User; // Votre classe User
import com.example.demo.repository.UserRepository; // Votre Repository JPA pour User
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class JpaUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository; // Suppose que vous avez un UserRepository

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // Chercher l'utilisateur dans la base de données
        User user = userRepository.findByUsername(username) // Suppose une méthode findByUsername dans UserRepository
                .orElseThrow(() -> new UsernameNotFoundException("User not found: " + username));

        // Mapper votre entité User vers un UserDetails de Spring Security
        // Ceci est un exemple simple, votre classe User peut implémenter UserDetails ou être mappée
        return org.springframework.security.core.userdetails.User.builder()
                .username(user.getUsername())
                .password(user.getPassword()) // Le mot de passe DOIT être encodé dans votre base de données
                .roles(user.getRoles().toArray(new String[0])) // Suppose une méthode getRoles() retournant List<String>
                .build();
    }
}
```

Vous devriez également configurer votre `SecurityFilterChain` pour utiliser l'authentification basée sur les formulaires ou une autre méthode appropriée, par exemple :

```java
package com.example.demo.security;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class FormLoginSecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authz -> authz
                .anyRequest().authenticated()
            )
            .formLogin(); // Utiliser l'authentification par formulaire

        return http.build();
    }
}
```

Spring Security utilisera automatiquement votre bean `UserDetailsService` pour charger les informations de l'utilisateur lors de la tentative de login via le formulaire.

#### Formulaires De Login Personnalisés

Au lieu d'utiliser le formulaire de login par défaut de Spring Security, vous pouvez spécifier votre propre page de login en utilisant `.formLogin().loginPage("/your-login-page").permitAll()`. N'oubliez pas d'autoriser l'accès à votre page de login pour les utilisateurs non authentifiés avec `.permitAll()`.

### Autorisation

L'autorisation détermine si un utilisateur authentifié a les permissions nécessaires pour accéder à une ressource ou effectuer une opération.

#### Sécurisation Des Endpoints Avec Les Annotations (@Secured, @PreAuthorize)

Vous pouvez sécuriser des méthodes spécifiques dans vos services ou contrôleurs en utilisant des annotations. Pour activer la sécurité basée sur les annotations, vous devez ajouter `@EnableMethodSecurity` à votre classe de configuration Spring Security.

```java
package com.example.demo.security;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.method.configuration.EnableMethodSecurity;

@Configuration
@EnableMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class MethodSecurityConfig {
    // Vos beans SecurityFilterChain vont ici
}
```

* `securedEnabled = true` active l'annotation `@Secured`.
* `prePostEnabled = true` active les annotations `@PreAuthorize` et `@PostAuthorize`.

**`@Secured`** : Utilisé pour spécifier une liste de rôles requis.

```java
import org.springframework.security.access.annotation.Secured;
import org.springframework.stereotype.Service;

@Service
public class SomeService {
    @Secured({"ROLE_ADMIN", "ROLE_MANAGER"}) // Seuls les ADMINs ou MANAGERs peuvent appeler cette méthode
    public String doAdminStuff() {
        return "Admin action performed";
    }
}
```

Notez le préfixe `ROLE_`.

**`@PreAuthorize`** : Permet d'utiliser des expressions Spring EL pour des règles d'autorisation plus complexes avant l'exécution de la méthode. C'est l'approche recommandée car elle est plus flexible.

```java
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.stereotype.Service;

@Service
public class AnotherService {
    // Seuls les utilisateurs ayant le rôle ADMIN ET le nom d'utilisateur 'system'
    @PreAuthorize("hasRole('ADMIN') and authentication.principal.username == 'system'")
    public String doHighlyRestrictedStuff() {
        return "Highly restricted action performed";
    }

    // Seuls les utilisateurs ayant le rôle USER
    @PreAuthorize("hasRole('USER')")
    public String doUserStuff() {
         return "User action performed";
    }

    // Uniquement l'utilisateur dont l'ID correspond à l'ID passé en argument
    @PreAuthorize("#userId == authentication.principal.id") // Suppose que UserDetails a un champ 'id'
    public User getUserProfile(Long userId) {
        // ... fetch user profile ...
        return null;
    }
}
```

`authentication.principal` fait référence à l'objet `UserDetails` de l'utilisateur authentifié.

#### Contrôle D'accès Basé Sur Les Rôles

La méthode la plus courante pour l'autorisation est de baser l'accès sur les rôles assignés à l'utilisateur (`ROLE_USER`, `ROLE_ADMIN`, etc.).

Dans la configuration `SecurityFilterChain`, vous pouvez utiliser `hasRole()` ou `hasAnyRole()` :

```java
package com.example.demo.security;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class RoleBasedSecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authz -> authz
                .requestMatchers("/admin/**").hasRole("ADMIN") // Seuls les ADMINs peuvent accéder aux chemins sous /admin
                .requestMatchers("/user/**").hasAnyRole("USER", "ADMIN") // USERs ou ADMINs peuvent accéder à /user
                .anyRequest().authenticated() // Toutes les autres requêtes nécessitent une authentification
            )
            .httpBasic(); // ou formLogin()

        return http.build();
    }
}
```

L'ordre des `requestMatchers` est important : les règles les plus spécifiques doivent être définies en premier. Notez que `hasRole()` ajoute automatiquement le préfixe `ROLE_`. Si vos rôles dans la base de données n'ont pas ce préfixe, utilisez `hasAuthority('ADMIN')` au lieu de `hasRole('ADMIN')`.

#### Expression-based Access Control

Les annotations `@PreAuthorize` et `@PostAuthorize`, ainsi que les configurations `requestMatchers` avec `.access()`, utilisent des expressions Spring EL pour définir des règles d'autorisation flexibles. Vous avez accès à l'objet `authentication`, aux paramètres de la méthode, aux beans Spring, etc.

Exemples d'expressions courantes :
* `isAuthenticated()` : L'utilisateur est authentifié (quel que soit son rôle).
* `isFullyAuthenticated()` : L'utilisateur n'est pas authentifié anonymement ou via "remember me".
* `isAnonymous()` : L'utilisateur n'est pas authentifié.
* `hasRole('ADMIN')` : L'utilisateur a le rôle 'ADMIN'.
* `hasAnyRole('USER', 'ADMIN')` : L'utilisateur a le rôle 'USER' ou 'ADMIN'.
* `hasAuthority('READ_PRIVILEGE')` : L'utilisateur a l'autorité spécifique 'READ_PRIVILEGE'.
* `hasAnyAuthority('READ_PRIVILEGE', 'WRITE_PRIVILEGE')`
* `permitAll()` : Autorise toutes les requêtes (aucun utilisateur requis).
* `denyAll()` : Refuse toutes les requêtes.
* `principal` : L'objet principal (généralement `UserDetails`) de l'utilisateur authentifié.
* `#paramName` : Fait référence à un paramètre de la méthode (utilisé avec `@PreAuthorize`/`@PostAuthorize`).

### JWT (JSON Web Tokens)

Les applications modernes, en particulier les API REST et les microservices, utilisent souvent des tokens pour l'authentification et l'autorisation plutôt que les sessions basées sur les cookies. JWT est un format de token populaire. Spring Security ne fournit pas d'implémentation complète pour émettre et valider des JWTs hors de la boîte dans le starter de base, mais il s'intègre bien avec des bibliothèques tierces (comme JJWT) ou avec Spring Security OAuth2 pour la gestion des JWTs.

#### Principes De Base

Un JWT est une chaîne compacte et auto-contenue (souvent en JSON) qui représente un ensemble de revendications (claims) entre deux parties. Il est typiquement utilisé pour transmettre de manière sécurisée des informations sur un utilisateur après qu'il se soit authentifié.

Le processus basique est le suivant :
1. L'utilisateur s'authentifie (par login/mot de passe, par exemple).
2. Le serveur valide les identifiants et émet un JWT contenant des informations sur l'utilisateur (son ID, ses rôles, etc.), souvent signé numériquement.
3. Le client stocke ce JWT (par exemple, dans le stockage local du navigateur).
4. Pour chaque requête subséquente vers le serveur pour accéder à des ressources protégées, le client inclut le JWT dans l'en-tête `Authorization` (généralement au format `Bearer <token>`).
5. Le serveur (ou une passerelle API) intercepte la requête, valide le JWT (sa signature, sa date d'expiration, etc.) et extrait les informations de l'utilisateur pour décider de l'autorisation.

#### Implémentation D'une Authentification Par Token

L'implémentation de l'authentification par JWT dans Spring Security implique plusieurs étapes :
1. Ajouter une dépendance pour générer/valider les JWTs (par exemple, JJWT).
2. Configurer Spring Security pour ne pas utiliser de session (`sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)`).
3. Créer un point d'entrée d'authentification (un contrôleur qui accepte les identifiants, appelle l'authentification Spring Security et génère un JWT).
4. Créer un filtre personnalisé qui intercepte les requêtes entrantes, extrait le JWT de l'en-tête `Authorization`, le valide, charge les détails de l'utilisateur (souvent sans aller en base de données si le JWT contient toutes les infos nécessaires) et configure le contexte de sécurité de Spring (`SecurityContextHolder`). Ce filtre doit être ajouté à la chaîne de filtres de Spring Security.

C'est un sujet plus avancé qui dépasse le cadre d'une simple introduction, mais voici un aperçu de la configuration de base sans session et l'ajout potentiel d'un filtre :

```java
package com.example.demo.security;

import com.example.demo.security.jwt.JwtRequestFilter; // Votre filtre JWT personnalisé
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@Configuration
public class JwtSecurityConfig {

    @Autowired
    private JwtRequestFilter jwtRequestFilter; // Votre filtre JWT

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable() // Souvent désactivé pour les API sans état
            .authorizeHttpRequests(authz -> authz
                .requestMatchers("/authenticate").permitAll() // Endpoint de login/token accessible à tous
                .anyRequest().authenticated() // Toutes les autres nécessitent un JWT valide
            )
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS); // Désactiver les sessions

        // Ajouter un filtre pour valider les JWTs avant le traitement de la requête
        http.addFilterBefore(jwtRequestFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
}
```

L'implémentation de `JwtRequestFilter`, de l'endpoint `/authenticate` et de la logique de génération/validation JWT est un travail non trivial qui nécessite une compréhension plus approfondie de Spring Security et des JWTs.

### En Pratique

Pour mettre en pratique ce que vous avez appris :

1. Créez un nouveau projet Spring Boot avec les dépendances `spring-boot-starter-web` et `spring-boot-starter-security`.
2. Démarrez l'application sans aucune configuration de sécurité personnalisée. Notez que vous êtes invité à vous connecter avec un formulaire par défaut ou une fenêtre HTTP Basic. Utilisez l'utilisateur `user` et le mot de passe généré dans les logs.
3. Créez une classe de configuration de sécurité (`@Configuration`) et ajoutez un bean `SecurityFilterChain` pour configurer vos propres règles (par exemple, autoriser `/public/**`, exiger une authentification pour `/private/**`).
4. Configurez l'authentification en mémoire avec quelques utilisateurs et rôles.
5. Créez un contrôleur avec quelques endpoints, certains accessibles à tous (`/public`), d'autres nécessitant une authentification (`/private`), et d'autres encore nécessitant des rôles spécifiques (`/admin`, `/user`).
6. Utilisez Postman, curl ou votre navigateur pour tester l'accès aux différents endpoints avec et sans authentification, et avec différents utilisateurs si vous avez configuré plusieurs rôles.

#### Tests De Sécurité

Spring Security fournit des utilitaires pour tester la sécurité. Vous pouvez utiliser `@WithMockUser` ou `@WithUserDetails` pour simuler un utilisateur authentifié dans vos tests de contrôleur (avec `@WebMvcTest` et `MockMvc`).

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.security.test.context.support.WithMockUser;
import org.springframework.test.web.servlet.MockMvc;
import org.junit.jupiter.api.Test;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(SecureController.class) // Tester uniquement le contrôleur
class SecureControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void publicEndpoint_shouldBeAccessible() throws Exception {
        mockMvc.perform(get("/public/hello"))
               .andExpect(status().isOk()); // Doit retourner 200 OK
    }

    @Test
    void privateEndpoint_withoutAuth_shouldBeForbidden() throws Exception {
        mockMvc.perform(get("/private/secret"))
               .andExpect(status().isUnauthorized()); // Doit retourner 401 Unauthorized
    }

    @Test
    @WithMockUser // Simule un utilisateur authentifié avec le rôle USER par défaut
    void privateEndpoint_withAuth_shouldBeAccessible() throws Exception {
        mockMvc.perform(get("/private/secret"))
               .andExpect(status().isOk()); // Doit retourner 200 OK
    }

    @Test
    @WithMockUser(roles = "ADMIN") // Simule un utilisateur ADMIN
    void adminEndpoint_withAdminRole_shouldBeAccessible() throws Exception {
         mockMvc.perform(get("/admin/dashboard"))
                .andExpect(status().isOk()); // Doit retourner 200 OK
    }

     @Test
     @WithMockUser(roles = "USER") // Simule un utilisateur USER
     void adminEndpoint_withUserRole_shouldBeForbidden() throws Exception {
          mockMvc.perform(get("/admin/dashboard"))
                 .andExpect(status().isForbidden()); // Doit retourner 403 Forbidden
     }
}
```

N'oubliez pas d'ajouter la dépendance `spring-security-test` (incluse dans `spring-boot-starter-test`) et d'importer les méthodes statiques nécessaires.

## Conclusion

Spring Security est un framework extrêmement puissant et flexible pour sécuriser vos applications Spring Boot. Il gère les complexités de l'authentification et de l'autorisation, vous permettant de vous concentrer sur la logique métier tout en assurant que votre application est protégée contre les menaces courantes.

Vous avez vu comment mettre en place une configuration de base, gérer l'authentification en mémoire ou avec une base de données via `UserDetailsService`, et comment sécuriser vos endpoints web ou vos méthodes de service en utilisant `SecurityFilterChain` et les annotations comme `@PreAuthorize`.

### Autres Aspects De Sécurité à Considérer

La sécurité est un vaste sujet. Au-delà des bases vues ici, d'autres aspects importants incluent :
* **CORS (Cross-Origin Resource Sharing) :** Comment gérer les requêtes provenant de domaines différents.
* **CSRF (Cross-Site Request Forgery) :** Spring Security fournit une protection intégrée contre les attaques CSRF pour les applications web basées sur session. Pour les API sans état (JWT), cela est souvent moins préoccupant côté serveur mais doit être géré côté client.
* **Gestion des sessions :** Comment Spring Security gère les sessions (pour les applications web avec état).
* **OAuth2 et OpenID Connect :** Pour l'authentification et l'autorisation déléguées (se connecter avec Google, Facebook, etc., ou sécuriser des API avec des tokens d'accès). Spring Security offre un support complet pour agir en tant que client, serveur de ressources ou serveur d'autorisation OAuth2.
* **Protection contre les attaques par force brute.**
* **Sécurisation des communications (HTTPS).**

### Ressources Pour Approfondir

Spring Security est un sujet complexe avec une documentation très complète. Pour aller plus loin, je vous recommande vivement :
* La documentation officielle de Spring Security.
* Les guides Spring sur la sécurité ([https://spring.io/guides/gs/securing-web/](https://spring.io/guides/gs/securing-web/), [https://spring.io/guides/gs/rest-service-cors/](https://spring.io/guides/gs/rest-service-cors/), etc.).
* Explorer les starters spécifiques comme `spring-boot-starter-oauth2-client` ou `spring-boot-starter-oauth2-resource-server`.

La sécurité est un apprentissage continu. En commençant avec les bases solides fournies par Spring Security, vous êtes bien équipé pour construire des applications Java sûres.
