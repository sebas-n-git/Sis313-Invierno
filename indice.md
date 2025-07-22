**SIS313 | Examen de Invierno**

---

## **Índice**

1. [Introducción](#introducción)
2. [Diagrama de Arquitectura](#diagrama-de-arquitectura)
3. [Comandos Clave Utilizados](#comandos-clave-utilizados)
    - [Configuración RAID 10](#configuración-raid-10)
    - [Simulación de Falla y Recuperación RAID 10](#simulación-de-falla-y-recuperación-raid-10)
    - [Configuración de MariaDB](#configuración-de-mariadb)
    - [Configuración de Firewall y Hardening](#configuración-de-firewall-y-hardening)
    - [Configuración de Nginx Balanceador](#configuración-de-nginx-balanceador)
    - [CRUD Node.js y Conectividad](#crud-nodejs-y-conectividad)
4. [Justificación de Tecnologías](#justificación-de-tecnologías)
5. [Pasos de Implementación](#pasos-de-implementación)
6. [Hardening Aplicado](#hardening-aplicado)
7. [Pruebas y Validaciones](#pruebas-y-validaciones)

---

## **Introducción**

Este proyecto simula una infraestructura web escalable con 5 instancias virtuales:  
- 1 Balanceador de carga (Nginx)
- 3 Servidores de aplicaciones Node.js (CRUD básico)
- 1 Servidor de base de datos (MariaDB, RAID 10 tolerante a fallos)  
Incluye hardening básico y evidencia de tolerancia a fallos.

---
