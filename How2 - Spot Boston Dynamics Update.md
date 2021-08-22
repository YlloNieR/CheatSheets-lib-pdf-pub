## How2 - Spot Boston Dynamics Update
# on Windows
## install / upgrade pip and pillow
```
py -3 -m pip install --upgrade pip
py -3 -m pip install --upgrade pillow
```

```
py.exe -3 -m pip install virtualenv
py.exe -3 -m virtualenv my_spot_v2_0_env
.\my_spot_v2_0_env\Scripts\activate.bat
```

## get bosdyn packages
```
py -3 -m pip install --upgrade bosdyn-client bosdyn-mission bosdyn-choreography-client
```
```
py -3 -m pip install bosdyn-client==2.2.0 bosdyn-mission==2.2.0 bosdyn-choreography-client==2.2.0
```

## check if isntallation successful
```
py -3 -m pip list --format=columns | findstr bosdyn
```

## look for spot on your network
```
ping 192.168.80.3
```

## get spots id
```
py -3 -m bosdyn.client 192.168.80.3 id
```

## debug if error occurs
```
py -3 -m bosdyn.client 192.168.80.3 id --verbose
```

## clone git repo
```
git clone https://github.com/boston-dynamics/spot-sdk.git
```

## install required dependencies
```
py -2 -m pip install -r requirements.txt
```