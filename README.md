Это пошаговое руководство поможет вам получить все необходимые учетные данные для работы с Spotify API.

## 1. Получение Spotify Client ID и Client Secret

### Шаг 1: Создайте приложение в Spotify Developer Dashboard
1. Перейдите на [Spotify Developer Dashboard](https://developer.spotify.com/dashboard/)
2. Войдите под своим аккаунтом Spotify (или создайте новый)
3. Нажмите "Create an App"

### Шаг 2: Заполните информацию о приложении
- **App Name**: Название вашего приложения (например, "My Discord RPC")
- **App Description**: Краткое описание
- **Redirect URI**: Добавьте `http://localhost:8888/callback` (это понадобится позже)

### Шаг 3: Получите Client ID и Client Secret
После создания приложения вы увидите:
- **Client ID** - скопируйте этот идентификатор
- **Client Secret** - нажмите "Show Client Secret" и скопируйте

## 2. Получение Access Token и Refresh Token

### Метод 1: Через Spotify Authorization Code Flow (рекомендуется)

1. **Сформируйте URL для авторизации**:
   ```
   https://accounts.spotify.com/authorize?
   client_id=ВАШ_CLIENT_ID&
   response_type=code&
   redirect_uri=http://localhost:8888/callback&
   scope=user-read-currently-playing%20user-read-playback-state&
   show_dialog=true
   ```

2. **Откройте этот URL в браузере** и авторизуйтесь

3. После авторизации вы будете перенаправлены на `http://localhost:8888/callback?code=ВАШ_КОД`

4. **Получите токены** с помощью этого кода (пример на Python):
   ```python
   import requests
   import base64

   client_id = "ВАШ_CLIENT_ID"
   client_secret = "ВАШ_CLIENT_SECRET"
   redirect_uri = "http://localhost:8888/callback"
   code = "КОД_ИЗ_ШАГА_3"

   # Формируем Basic Auth header
   auth_header = base64.b64encode(f"{client_id}:{client_secret}".encode()).decode()

   response = requests.post(
       "https://accounts.spotify.com/api/token",
       data={
           "grant_type": "authorization_code",
           "code": code,
           "redirect_uri": redirect_uri
       },
       headers={
           "Authorization": f"Basic {auth_header}",
           "Content-Type": "application/x-www-form-urlencoded"
       }
   )

   tokens = response.json()
   print("Access Token:", tokens["access_token"])
   print("Refresh Token:", tokens["refresh_token"])
   ```

### Метод 2: Через Implicit Grant Flow (проще, но менее безопасно)

1. **Сформируйте URL**:
   ```
   https://accounts.spotify.com/authorize?
   client_id=ВАШ_CLIENT_ID&
   response_type=token&
   redirect_uri=http://localhost:8888/callback&
   scope=user-read-currently-playing%20user-read-playback-state&
   show_dialog=true
   ```

2. Откройте URL в браузере и авторизуйтесь

3. После перенаправления вы увидите Access Token в URL:
   ```
   http://localhost:8888/callback#access_token=ВАШ_ACCESS_TOKEN&...
   ```

4. Refresh Token этим методом не получить - используйте первый метод

## 3. Настройка вашего приложения

Вставьте полученные значения в настройках приложения



## Важные замечания

1. **Refresh Token**:
   - Действует до тех пор, пока не будет использован новый
   - Позволяет получать новые Access Tokens без повторной авторизации
   - Сохраните его в безопасном месте

2. **Access Token**:
   - Действует 1 час
   - Автоматически обновляется с помощью Refresh Token

3. **Безопасность**:
   - Никогда не публикуйте Client Secret и Refresh Token в открытом доступе
   - Используйте .gitignore для конфигурационных файлов

## Альтернативные инструменты

Если вам неудобно использовать код, попробуйте:
- [Spotify Token Swap](https://github.com/spotify/spotify-token-swap)
- [OAuth 2.0 Playground](https://oauth2.spotify.com/authorize)

Теперь у вас есть все необходимые учетные данные для работы с Spotify API!
