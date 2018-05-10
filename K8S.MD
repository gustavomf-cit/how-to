```python
#!/usr/bin/env python3
from kubernetes import client, config
from kubernetes.stream import stream
from kubernetes.client import CoreV1Api

config.load_kube_config()
api = CoreV1Api()

exec_command = ['/bin/sh', '-c', 'ls', '-la']

name = 'podname'


exec_command = ['/bin/sh']
resp = stream(api.connect_get_namespaced_pod_exec, name, 'default',
              command=exec_command,
              stderr=True, stdin=True,
              stdout=True, tty=False,
              _preload_content=False)

if resp.is_open():
    resp.write_stdin('ls -la\n')
    while resp.is_open():
        resp.update(timeout=1)
        if resp.peek_stdout():
            print(resp.read_stdout())
            break

```
