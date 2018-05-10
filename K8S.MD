```python
#!/usr/bin/env python3
from kubernetes import client, config
from kubernetes.stream import stream
from kubernetes.client import CoreV1Api

config.load_kube_config()
api = CoreV1Api()

name = 'ipodname-0'

exec_command = ['/bin/sh', '-c', 'ls -la']


resp = stream(api.connect_get_namespaced_pod_exec, name, 'default',
              command=exec_command,
              stderr=True, stdin=True,
              stdout=True, tty=False,
              _preload_content=False)

txt_buffer = ''

while resp.is_open():
    resp.update(timeout=1)
    if resp.peek_stdout():
        out = resp.read_stdout()
        print("bytes received: %s" % len(out))
        txt_buffer = out
        break
    if resp.peek_stderr():
        print("STDERR: %s" % resp.read_stderr())

resp.close()
print(txt_buffer)

```
