# Pasos para iniciar proyecto

Primero tener descargado sqlite e iniciar el ejecutable de sqlite3.
[Link de descarga del .zip](https://www.sqlite.org/2023/sqlite-tools-win-x64-3440200.zip)


1. `pip install -r requirements.txt`
2. `python manage.py makemigrations`
3. `python manage.py migrate`
4. `python manage.py createsuperuser`
5. `python manage.py runserver`
6. Ingresar a tu navegador en el puerto 8000 [http://127.0.0.1:8000/](http://127.0.0.1:8000/)


# PROGRESO DE DISEÑO 

Se diseño una nueva intefas para el login.

```python

<style>
@media screen and (min-width: 480px) {
    #form {
        width: 400px;
    }
}

@-webkit-keyframes animatebottom {
    0% {
        opacity: 0;
        margin-top: 500px;
    }
    100% {
        opacity: 1;
        margin-top: 0px;
    }
}

@keyframes animatebottom {
    0% {
        opacity: 0;
        margin-top: 500px;
    }
    100% {
        opacity: 1;
        margin-top: 0px;
    }
}

input {
    color: #888;
    margin: 5px;
    border-radius: 0px;
    border: 0px solid #eee;
    padding: 8px;
    background: #eee;
    transition-duration: .5s;
}

input:focus {
    border-radius: 50px;
    background-color: #ccc;
    color: #fff;
}

a {
    text-decoration: none !important;
    color: #fff;
}

hr {
    border: 2px solid #c1c1c1;
}

button {
    cursor: hand;
    border: none;
    background: #27a5df;
    color: white;
    padding: 11px 26px;
    border-radius: 5px;
    font-size: 16px;
    transition-duration: .1s;
}

button:active {
    background-color: #555;
}

* {
    outline: none;
    user-select: none;
}

h1 {
    width: 100%;
    color: #888;
}

#form {
    outline: 15px solid #4bbaed;
    margin: 20px;
    border-radius: 10px;
    text-align: -webkit-center;
    background: rgba(255, 255, 255, 1);
    padding: 30px 10px;
    border-top: 30px solid #27a5df;
    border-bottom: 30px solid #27a5df;
    box-shadow: -1px 1px 20px rgba(0, 0, 0, 0.5);
    border: none;
    outline: none;
    animation: animatebottom 1s cubic-bezier(1, -0.06, 0.26, 1.06);
}

body {
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    background-image: url("https://images.pexels.com/photos/1587/blurred-background.jpg?auto=compress&cs=tinysrgb&dpr=2&w=5000");
    background-attachment: fixed;
    background-position: bottom left;
    background-repeat: no-repeat;
    background-size: cover;
    transition: 1s;
    padding: 10px;
    margin: 0;
    color: #888;
    font-family: Consolas, "Andale Mono", "Lucida Console", "Lucida Sans Typewriter", Monaco, "Courier New", "monospace";
    text-align: -webkit-center;
}

/* Changed footer - August 2020 */
footer {
    padding: 15px 15px;
    width: 80%;
    position: fixed;
    bottom: 2%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    text-align: -webkit-center;
    color: #fff!important;
    border-radius: 50px;
    color: #fff;
    background-color: rgba(255, 255, 255, 0.25);
}

header h1 {
    width: 90%;
    border: 5px dotted #27a5df;
    color: #27a5df;
    text-align: -webkit-center;
    border-radius: 5px;
}

</style>
```

### CASOS DE PRUEBA

Se hizo una prueba de test para comprobar la existencia de usuarios y la de no usuarios.


```python
class TasksTestCase(LiveServerTestCase):
    def setUp(self):
        # Configuración del navegador Selenium (puedes ajustar según tus necesidades)
        chrome_options = Options()
        # chrome_options.add_argument("--headless")  # Ejecución en modo sin cabeza para pruebas en segundo plano
        self.selenium = webdriver.Chrome(options=chrome_options)
        self.selenium.implicitly_wait(10)  # Espera implícita de 10 segundos
        super(TasksTestCase, self).setUp()

    def tearDown(self):
        self.selenium.quit()
        super(TasksTestCase, self).tearDown()

    def test_tasks_page(self):
        # Crear un usuario y tareas para ese usuario
        user = User.objects.create_user(username='testuser', password='testpass')
        Task.objects.create(user=user, description='Task 1')

        # Acceder a la página de login para verificar un usuario existente.
        self.selenium.get(f'{self.live_server_url}/signin/')
        self.selenium.find_element(By.NAME, 'username').send_keys('marisol')
        self.selenium.find_element(By.NAME, 'password').send_keys('123456')
        self.selenium.find_element(By.NAME, 'password').send_keys(Keys.ENTER)
    
        # Verificar que la página de tareas se carga correctamente
        self.assertIn('tasks', self.selenium.find_element(By.CSS_SELECTOR, "h1").text.lower())

        WebDriverWait(self.selenium, 10).until(
            EC.presence_of_element_located((By.TAG_NAME, 'progress'))
        )

        # Acceder a la página de login para verificar un usuario no existente.
        self.selenium.get(f'{self.live_server_url}/signin/')
        self.selenium.find_element(By.NAME, 'username').send_keys('marisol')
        self.selenium.find_element(By.NAME, 'password').send_keys('123')
        self.selenium.find_element(By.NAME, 'password').send_keys(Keys.ENTER)
    
        # Verificar que la página de tareas se carga correctamente
        self.assertIn('tasks', self.selenium.find_element(By.CSS_SELECTOR, "h1").text.lower())

        WebDriverWait(self.selenium, 10).until(
            EC.presence_of_element_located((By.TAG_NAME, 'progress'))
        )

        progress_message = self.selenium.find_element(By.ID, 'progress-message')
        self.assertIn('Completed 0/0', progress_message.text)
```
