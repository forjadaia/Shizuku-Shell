# Shizuku Shell Local

App Android open source, licenciado em **MIT**, para executar comandos `adb shell` locais usando a API do **Shizuku** e mostrar a saída do comando na tela.

O app aceita comandos com ou sem o prefixo `adb shell`:

```bash
adb shell id
```

ou:

```bash
id
```

A tela mostra:

- comando executado;
- `exitCode`;
- `STDOUT`;
- `STDERR`.

---

## O que tem neste repositório

Este ZIP/repositório contém somente o necessário para estudar, modificar e compilar o app:

```text
.
├── LICENSE
├── README.md
├── .gitignore
├── build.gradle
├── settings.gradle
├── gradle.properties
├── gradlew
├── gradlew.bat
├── gradle/wrapper/
├── key.properties.example
└── app/
    ├── build.gradle
    └── src/main/
        ├── AndroidManifest.xml
        ├── java/com/arena/shizuku_shell_local/
        │   ├── MainActivity.java
        │   └── NativeMarker.java
        ├── cpp/
        │   ├── CMakeLists.txt
        │   └── native_marker.cpp
        └── res/
            ├── mipmap-*/ic_launcher.png
            └── values/
```

Coisas que **não** ficam no GitHub porque são geradas ou secretas:

```text
build/
.gradle/
app/build/
app/.cxx/
*.apk
*.aab
*.jks
*.keystore
key.properties
local.properties
```

Esses arquivos são recriados automaticamente quando você compila.

---

## Licença

Este projeto usa a licença **MIT**. Veja o arquivo [`LICENSE`](LICENSE).

Você pode:

- usar;
- estudar;
- modificar;
- redistribuir;
- criar forks;
- publicar versões modificadas;
- usar comercialmente.

A única exigência principal é manter o aviso de copyright e a licença MIT nas cópias do projeto.

---

## Requisitos para compilar

Você precisa de:

- Android Studio **ou** Android SDK Command Line Tools;
- JDK 17 ou superior;
- Android SDK Platform 36;
- Android Build Tools 36.x;
- Android NDK `28.2.13676358`;
- CMake `3.22.1`.

No Android Studio, instale pelo menu:

```text
Settings > Android SDK > SDK Platforms
Settings > Android SDK > SDK Tools
```

Marque:

```text
Android SDK Platform 36
Android SDK Build-Tools
NDK (Side by side)
CMake
Android SDK Platform-Tools
```

---

## Como abrir para codar

### Opção 1: Android Studio

1. Extraia o ZIP.
2. Abra o Android Studio.
3. Clique em **Open**.
4. Selecione a pasta do projeto.
5. Aguarde o Gradle sincronizar.
6. Edite os arquivos dentro de:

```text
app/src/main/java/com/arena/shizuku_shell_local/
```

### Opção 2: VS Code ou editor simples

Você pode editar os arquivos `.java`, `.cpp`, `.xml` e `.gradle` em qualquer editor.

Para compilar pelo terminal:

```bash
./gradlew assembleRelease
```

No Windows:

```bat
gradlew.bat assembleRelease
```

---

## Como compilar APKs

Na raiz do projeto:

```bash
./gradlew assembleRelease
```

Os APKs ficam em:

```text
app/build/outputs/apk/release/
```

O projeto gera APK separado por arquitetura:

```text
armeabi-v7a  = ARM32
arm64-v8a    = ARM64
```

Dentro dos APKs fica a biblioteca nativa própria do app:

```text
lib/armeabi-v7a/libshizuku_shell_local.so
lib/arm64-v8a/libshizuku_shell_local.so
```

---

## Como instalar no celular

Com o celular conectado por USB e depuração USB ativada:

```bash
adb install app/build/outputs/apk/release/app-arm64-v8a-release.apk
```

Ou, para celular ARM32:

```bash
adb install app/build/outputs/apk/release/app-armeabi-v7a-release.apk
```

Se já tiver uma versão instalada:

```bash
adb install -r app/build/outputs/apk/release/app-arm64-v8a-release.apk
```

---

## Como usar o app

1. Instale o app **Shizuku** no celular.
2. Inicie o serviço Shizuku.
3. Instale este app.
4. Abra este app.
5. Toque em **Permissão**.
6. Autorize no Shizuku.
7. Digite um comando.
8. Toque em **Executar**.

Exemplos seguros para teste:

```bash
adb shell id
adb shell whoami
adb shell getprop ro.build.version.release
adb shell pm list packages
adb shell settings list global
```

---

## Como o código funciona

### `MainActivity.java`

Arquivo principal do app:

```text
app/src/main/java/com/arena/shizuku_shell_local/MainActivity.java
```

Ele faz quatro coisas principais:

1. Cria a interface na mão usando Java Android nativo.
2. Verifica se o Shizuku está ativo.
3. Pede permissão ao Shizuku.
4. Executa comandos usando o processo remoto do Shizuku.

A função mais importante é:

```java
private String executeViaShizukuShell(String command)
```

Ela executa:

```text
/system/bin/sh -c "comando"
```

via Shizuku e captura:

- saída normal (`stdout`);
- saída de erro (`stderr`);
- código de saída (`exitCode`).

### Permissão Shizuku

A permissão é declarada em:

```text
app/src/main/AndroidManifest.xml
```

Com:

```xml
<uses-permission android:name="moe.shizuku.manager.permission.API_V23" />
```

E o provider oficial do Shizuku:

```xml
<provider
    android:name="rikka.shizuku.ShizukuProvider"
    android:authorities="${applicationId}.shizuku"
    android:multiprocess="false"
    android:enabled="true"
    android:exported="true"
    android:permission="android.permission.INTERACT_ACROSS_USERS_FULL" />
```

### Biblioteca nativa `.so`

O app tem uma lib nativa própria para o APK conter `lib/` com `.so` do app:

```text
app/src/main/cpp/native_marker.cpp
```

O nome da biblioteca é definido em:

```text
app/src/main/cpp/CMakeLists.txt
```

Aqui:

```cmake
add_library(shizuku_shell_local SHARED native_marker.cpp)
```

Isso gera:

```text
libshizuku_shell_local.so
```

---

## Como modificar a interface

A interface está em Java, dentro de:

```text
MainActivity.java
```

Procure a função:

```java
private void buildUi()
```

Lá você pode mudar:

- textos;
- cores;
- botões;
- tamanho das fontes;
- campos de entrada;
- layout.

Exemplo: para mudar o título, procure:

```java
title.setText("Shizuku Shell\nADB shell local via Shizuku");
```

E troque para o texto que quiser.

---

## Como modificar o comando padrão

No arquivo `MainActivity.java`, procure:

```java
commandEdit.setText("adb shell id && whoami && getprop ro.build.version.release");
```

Troque pelo comando inicial desejado.

---

## Como mudar o ícone

Os ícones ficam em:

```text
app/src/main/res/mipmap-mdpi/ic_launcher.png
app/src/main/res/mipmap-hdpi/ic_launcher.png
app/src/main/res/mipmap-xhdpi/ic_launcher.png
app/src/main/res/mipmap-xxhdpi/ic_launcher.png
app/src/main/res/mipmap-xxxhdpi/ic_launcher.png
```

Para trocar o ícone, substitua esses arquivos mantendo o mesmo nome:

```text
ic_launcher.png
```

Tamanhos comuns:

```text
mipmap-mdpi     48x48
mipmap-hdpi     72x72
mipmap-xhdpi    96x96
mipmap-xxhdpi   144x144
mipmap-xxxhdpi  192x192
```

---

## Como mudar nome do app

No arquivo:

```text
app/src/main/AndroidManifest.xml
```

Procure:

```xml
android:label="Shizuku Shell"
```

Troque para o nome desejado.

---

## Como mudar package name / applicationId

O package atual é:

```text
com.arena.shizuku_shell_local
```

Para mudar corretamente:

1. Em `app/build.gradle`, altere:

```gradle
namespace 'com.arena.shizuku_shell_local'
applicationId 'com.arena.shizuku_shell_local'
```

2. Renomeie a pasta Java:

```text
app/src/main/java/com/arena/shizuku_shell_local/
```

3. Altere a primeira linha dos arquivos `.java`:

```java
package com.arena.shizuku_shell_local;
```

4. Se mudar o package, também ajuste o nome JNI em `native_marker.cpp` se alterar a classe `NativeMarker`.

---

## Como mudar versão

No arquivo:

```text
app/build.gradle
```

Procure:

```gradle
versionCode 1
versionName '1.0.0'
```

Exemplo para versão 1.0.1:

```gradle
versionCode 2
versionName '1.0.1'
```

---

## Como assinar com sua própria keystore

Por segurança, este ZIP **não inclui a keystore real**.

Para gerar sua keystore:

```bash
keytool -genkeypair -v \
  -keystore app/shizuku_shell_release.jks \
  -storetype PKCS12 \
  -alias shizuku_shell_release \
  -keyalg RSA \
  -keysize 4096 \
  -validity 10000
```

Depois copie:

```bash
cp key.properties.example key.properties
```

Edite `key.properties`:

```properties
storePassword=SUA_SENHA_DA_KEYSTORE
keyPassword=SUA_SENHA_DA_CHAVE
keyAlias=shizuku_shell_release
storeFile=app/shizuku_shell_release.jks
```

Depois compile:

```bash
./gradlew assembleRelease
```

Nunca publique no GitHub:

```text
key.properties
app/*.jks
app/*.keystore
```

Se alguém tiver sua keystore, essa pessoa pode assinar APKs como se fossem seus.

---

## Como verificar os `.so` dentro do APK

Depois de compilar:

```bash
unzip -l app/build/outputs/apk/release/app-arm64-v8a-release.apk 'lib/*/*.so'
```

Deve aparecer algo como:

```text
lib/arm64-v8a/libshizuku_shell_local.so
```

Para ARM32:

```bash
unzip -l app/build/outputs/apk/release/app-armeabi-v7a-release.apk 'lib/*/*.so'
```

Deve aparecer:

```text
lib/armeabi-v7a/libshizuku_shell_local.so
```

---

## Como subir no GitHub

Depois de extrair o ZIP:

```bash
git init
git add .
git commit -m "Initial open source release"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git
git push -u origin main
```

Antes de dar `git add .`, confirme que estes arquivos não existem ou não serão enviados:

```bash
git status
```

Não envie:

```text
key.properties
app/*.jks
*.apk
app/build/
app/.cxx/
.gradle/
```

O `.gitignore` já bloqueia esses arquivos.

---

## Aviso de segurança

Este app executa comandos com as permissões fornecidas pelo Shizuku. Comandos ADB shell podem alterar configurações do sistema, listar pacotes, parar apps e fazer outras ações sensíveis.

Use apenas comandos que você entende.

Este projeto é para aprendizado, automação local e uso consciente.
