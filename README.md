# Django ToDo list

This is a todo list web application with basic features of most web apps, i.e., accounts/login, API, and interactive UI. To do this task, you will need:

- CSS | [Skeleton](http://getskeleton.com/)
- JS  | [jQuery](https://jquery.com/)

## Explore

Try it out by installing the requirements (the following commands work only with Python 3.8 and higher, due to Django 4):

```
pip install -r requirements.txt
```

Create a database schema:

```
python manage.py migrate
```

And then start the server (default is http://localhost:8000):

```
python manage.py runserver
```

Now you can browse the [API](http://localhost:8000/api/) or start on the [landing page](http://localhost:8000/).

## Task

Create a kubernetes manifest for a pod which will containa ToDo app container:

1. Fork this repository.
1. Use `kind` to spin up a cluster from a `cluster.yml` configuration file.
1. Run bootstrap.sh to deploy the app to the cluster and to install ingress controller.
1. Create a `ingress.yml` file for `Ingress` to manage ingress traffic.
1. `Ingress` requirement:
    1. `Ingress` file should be located in `./infrastructure/ingress` directory
    2. `Ingress` should have 1 http rule
    3. `Ingress` should have 1 path based rule
    4. Rule should route traffic to the app on `/` path
    5. Rule should capture path and forward it to the app.  `path: {your_regex}`
1. After ingress deployment you should be able to access the app on `http://localhost` and see the app running.
1. There should not be any requests failing with 404 status code in browser console.
1. `README.md` should have instructuions on how to validate the changes
1. Create PR with your changes and attach it for validation on a platform.

---

### How to validate:

```bash
kubectl get ingress -n todoapp
```
```yaml
NAME          CLASS    HOSTS   ADDRESS     PORTS   AGE
tda-ingress   <none>   *       localhost   80      10m
```

```bash
kubectl describe ingress tda-ingress -n todoapp
```
```yaml
Name:             tda-ingress
Labels:           <none>
Namespace:        todoapp
Address:          localhost
Ingress Class:    <none>
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /   todoapp-service:80 (10.244.1.2:8080,10.244.2.4:8080)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:
  Type    Reason  Age                From                      Message
  ----    ------  ----               ----                      -------
  Normal  Sync    11m (x2 over 11m)  nginx-ingress-controller  Scheduled for sync
```

Now you can start on the [landing page](http://localhost/) or browse the [API](http://localhost:/api/)

---

Visited:
 - `http://localhost/`
 - `http://localhost:/api/`
 - `http://localhost:/api/heath`

Get ingress pod name:
```bash
kubectl get pods -n ingress-nginx
```
```yaml
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-5zpqt        0/1     Completed   0          65m
ingress-nginx-admission-patch-5qqvv         0/1     Completed   1          65m
ingress-nginx-controller-56cbc5d9d4-82xq5   1/1     Running     0          65m
```

View logs:
```bash
kubectl logs ingress-nginx-controller-56cbc5d9d4-82xq5 -n ingress-nginx
```
```yaml
172.18.0.1 - - [08/Jul/2024:10:27:02 +0000] "GET / HTTP/1.1" 200 3747 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36" 816 0.019 [todoapp-todoapp-service-80] [] 10.244.1.2:8080 3747 0.019 200 f4d30fe9a4ae61d4379095042778f51b
172.18.0.1 - - [08/Jul/2024:10:27:20 +0000] "GET /api/ HTTP/1.1" 200 5592 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36" 820 0.010 [todoapp-todoapp-service-80] [] 10.244.2.4:8080 5592 0.010 200 4b9b01e5275d1ea4927e5eb699bdcaf7
172.18.0.1 - - [08/Jul/2024:10:27:32 +0000] "GET /api/health HTTP/1.1" 200 9 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36" 774 0.056 [todoapp-todoapp-service-80] [] 10.244.1.2:8080 9 0.056 200 fa45e7bd54c53009d2475dc9662102e3
```

All 3 `GET`'s are `200`
