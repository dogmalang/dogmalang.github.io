# Introducción a Dogma

**Dogma** es un lenguaje de programación de *scripts*, cuyas principales características son:

- Es fácil de aprender y de usar.
- Se puede usar gratuitamente.
- Compila a otros lenguajes. Actualmente, a **JavaScript** y, próximamemente, a **Python** también.
- Es multiparadigma, permitiendo la programación orientada a objetos, asíncrona, funcional e imperativa.
- Usa tipado dinámico.
- Utiliza el sangrado para delimitar los bloques de código, de manera similar a como hace **Python**.
- Busca reducir las líneas de código necesarias a escribir.
- Soporta *mixins* de manera nativa.
- Soporta **React** de manera nativa.

Sus referencias fundamentales son *C*, *C++*, *C#*, *Dart*, *F#*, *Go*, *JavaScript*, *Julia*, *Kotlin*, *Lua*, *PL/pgSQL*, *Python*, *Ruby*, *Rust*, *T-SQL*, *TypeScript* y *VBA*.

## Instalación

El entorno de desarrollo consiste en:

- `dogmac`, el compilador de **Dogma**.
- `language-dogma`, el paquete para el coloreado de código para [Atom](https://atom.io/).

### dogmac

El compilador de **Dogma** es `dogmac`.
Se encuentra escrito en **Lua**, requiriendo **Lua 5.3** y **LuaRocks 2.4**.
Es **100% code coverage**.

#### Instalación de Lua

Por favor, lea atentamente las instrucciones y, después, pase a ejecutarlas.
Si además se siente cómodo con [Docker](https://www.docker.com/), le recomendamos pruebe primero la instalación en un contenedor; en cuyo caso, puede comenzar haciendo lo siguiente:

```
$ sudo docker run --name ubu18 -it ubuntu:18.04 /bin/bash
```

La manera más sencilla de instalar [Lua](https://www.lua.org/), en distribuciones [Ubuntu](https://www.ubuntu.com), es mediante el paquete `lua5.3`.
Según la versión de **Ubuntu**, puede estar disponible la versión `5.3.1` ó `5.3.3`, siendo la `5.3.5` la última en el momento de escribir estas líneas.
Para comprobar la versión disponible:

```
$ sudo apt update
$ apt search lua5.3
```

Para instalar:

```
$ sudo apt install -y lua5.3 liblua5.3-dev
```

Una vez instalado, se recomienda comprobar que hay acceso a `lua`:

```
$ lua5.3 -v
$ lua -v
```

Es muy probable que no funcione `lua`, pero sí, `lua5.3`.
En este caso, crear un enlace de `lua` a `lua5.3`:

```
$ sudo ln -s $(which lua5.3) $(dirname $(which lua5.3))/lua
$ lua -v
```

#### Instalación de LuaRocks

`dogmac` se encuentra disponible mediante el empaquetador [LuaRocks](https://luarocks.org), similar a [NPM](https://www.npmjs.com/) de [Node](https://nodejs.org).
En sistemas **Ubuntu**, el paquete a instalar es `luarocks`, versión `2.4` o superior:

```
$ apt search luarocks
$ sudo apt install -y luarocks
```

Es buena práctica comprobar que hay acceso al comando `luarocks`:

```
$ luarocks help
```

Finalmente, añadir el directorio `~/.luarocks/bin` al **PATH** del usuario:

```
$ echo 'export PATH=$PATH:~/.luarocks/bin' >> ~/.bashrc
$ . ~/.bashrc
```

#### Instalación de dogmac

Una vez instalados `lua` y `luarocks`, lo siguiente es instalar `dogmac`:

```
$ luarocks install --local dogmac
```

Comprobar que tenemos acceso al comando `dogmac`:

```
$ dogmac -v
$ dogmac -h
```

### Paquete language-dogma de Atom

Se ha desarrollado el paquete `language-dogma` para los usuarios de **Atom**.
Con él instalado, el código aparecerá coloreado.
Para su instalación, use `Edit > Preferences > Install`.
