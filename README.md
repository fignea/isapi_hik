# 📡 Guía Completa de ISAPI para Hikvision

## ¿Qué es ISAPI?

**ISAPI (Internet Server Application Programming Interface)** es la API REST de Hikvision que permite controlar y monitorear dispositivos de videovigilancia, porteros automáticos, y sistemas de control de acceso a través de comandos HTTP/HTTPS.

## 🔧 Configuración Básica

### Credenciales del Dispositivo
```python
# Configuración en config.py
PORTERO_CONFIG = {
    'ip': '192.168.0.103',
    'username': 'admin',
    'password': 'German9876',
    'isapi_port': 80,
    'http_port': 80
}
```

### Autenticación
ISAPI utiliza **HTTP Digest Authentication**:
```python
from requests.auth import HTTPDigestAuth
import requests

session = requests.Session()
session.auth = HTTPDigestAuth('admin', 'German9876')
session.verify = False  # Para HTTPS con certificados autofirmados
```

## 🚪 Control de Puerta - Ejemplos Prácticos

### 1. Abrir Puerta
```python
def open_door(door_id=1):
    """Abre la puerta especificada"""
    url = f"http://192.168.0.103/ISAPI/AccessControl/RemoteControl/door/{door_id}"
    
    # XML para abrir puerta
    xml_data = '''<?xml version="1.0" encoding="UTF-8"?>
<RemoteControlDoor>
    <cmd>open</cmd>
</RemoteControlDoor>'''
    
    headers = {
        'Content-Type': 'application/xml',
        'Accept': 'application/xml'
    }
    
    response = session.put(url, data=xml_data, headers=headers, timeout=10)
    
    if response.status_code == 200:
        print("✅ Puerta abierta exitosamente")
        return True
    else:
        print(f"❌ Error: {response.status_code} - {response.text}")
        return False
```

### 2. Cerrar Puerta
```python
def close_door(door_id=1):
    """Cierra la puerta especificada"""
    url = f"http://192.168.0.103/ISAPI/AccessControl/RemoteControl/door/{door_id}"
    
    # XML para cerrar puerta
    xml_data = '''<?xml version="1.0" encoding="UTF-8"?>
<RemoteControlDoor>
    <cmd>close</cmd>
</RemoteControlDoor>'''
    
    headers = {
        'Content-Type': 'application/xml',
        'Accept': 'application/xml'
    }
    
    response = session.put(url, data=xml_data, headers=headers, timeout=10)
    
    if response.status_code == 200:
        print("✅ Puerta cerrada exitosamente")
        return True
    else:
        print(f"❌ Error: {response.status_code} - {response.text}")
        return False
```

### 3. Consultar Estado de Puerta
```python
def get_door_status(door_id=1):
    """Obtiene el estado de la puerta"""
    url = f"http://192.168.0.103/ISAPI/AccessControl/RemoteControl/door/{door_id}"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("✅ Estado de puerta consultado")
        print(f"Respuesta: {response.text}")
        return True
    else:
        print(f"❌ Error consultando estado: {response.status_code}")
        return False
```

## 📊 Utilidades ISAPI Avanzadas

### 1. Información del Dispositivo
```python
def get_device_info():
    """Obtiene información completa del dispositivo"""
    url = "http://192.168.0.103/ISAPI/System/deviceInfo"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("📱 Información del dispositivo:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

### 2. Estado del Sistema
```python
def get_system_status():
    """Obtiene el estado del sistema"""
    url = "http://192.168.0.103/ISAPI/System/status"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("🖥️ Estado del sistema:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

### 3. Configuración de Red
```python
def get_network_config():
    """Obtiene la configuración de red"""
    url = "http://192.168.0.103/ISAPI/System/Network/interfaces"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("🌐 Configuración de red:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

### 4. Eventos del Dispositivo
```python
def get_events():
    """Obtiene eventos del dispositivo"""
    url = "http://192.168.0.103/ISAPI/Event/notification/alertStream"
    
    response = session.get(url, timeout=10)
    
    if response.status_code == 200:
        print("📢 Eventos del dispositivo:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

### 5. Configuración de Cámaras
```python
def get_camera_config():
    """Obtiene configuración de cámaras"""
    url = "http://192.168.0.103/ISAPI/System/Video/inputs/channels"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("📹 Configuración de cámaras:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

### 6. Control de PTZ (Pan-Tilt-Zoom)
```python
def ptz_control(channel=1, action="start", speed=5):
    """Controla movimiento PTZ de la cámara"""
    url = f"http://192.168.0.103/ISAPI/PTZCtrl/channels/{channel}/continuous"
    
    # XML para control PTZ
    xml_data = f'''<?xml version="1.0" encoding="UTF-8"?>
<PTZData>
    <pan>{speed if action == "left" or action == "right" else 0}</pan>
    <tilt>{speed if action == "up" or action == "down" else 0}</tilt>
    <zoom>0</zoom>
</PTZData>'''
    
    headers = {
        'Content-Type': 'application/xml',
        'Accept': 'application/xml'
    }
    
    response = session.put(url, data=xml_data, headers=headers, timeout=5)
    
    if response.status_code == 200:
        print(f"✅ Control PTZ {action} ejecutado")
        return True
    else:
        print(f"❌ Error PTZ: {response.status_code}")
        return False
```

### 7. Configuración de Almacenamiento
```python
def get_storage_info():
    """Obtiene información de almacenamiento"""
    url = "http://192.168.0.103/ISAPI/ContentMgmt/Storage"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("💾 Información de almacenamiento:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

### 8. Configuración de Usuarios
```python
def get_users():
    """Obtiene lista de usuarios"""
    url = "http://192.168.0.103/ISAPI/Security/users"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("👥 Usuarios del sistema:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

### 9. Configuración de Alarmas
```python
def get_alarm_config():
    """Obtiene configuración de alarmas"""
    url = "http://192.168.0.103/ISAPI/Event/triggers"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("🚨 Configuración de alarmas:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

### 10. Configuración de Audio
```python
def get_audio_config():
    """Obtiene configuración de audio"""
    url = "http://192.168.0.103/ISAPI/System/Audio/inputs"
    
    response = session.get(url, timeout=5)
    
    if response.status_code == 200:
        print("🔊 Configuración de audio:")
        print(response.text)
        return response.text
    else:
        print(f"❌ Error: {response.status_code}")
        return None
```

## 🔧 Clase Completa de Cliente ISAPI

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Cliente ISAPI completo para Hikvision
"""

import requests
import time
import logging
from requests.auth import HTTPDigestAuth
import xml.etree.ElementTree as ET

class HikvisionISAPIClient:
    def __init__(self, device_ip="192.168.0.103", username="admin", password="German9876"):
        self.device_ip = device_ip
        self.username = username
        self.password = password
        self.base_url = f"http://{device_ip}"
        self.session = requests.Session()
        self.session.auth = HTTPDigestAuth(username, password)
        self.session.verify = False
        
    def test_connection(self):
        """Prueba la conexión al dispositivo"""
        try:
            url = f"{self.base_url}/ISAPI/System/deviceInfo"
            response = self.session.get(url, timeout=5)
            return response.status_code == 200
        except:
            return False
    
    def open_door(self, door_id=1):
        """Abre la puerta especificada"""
        url = f"{self.base_url}/ISAPI/AccessControl/RemoteControl/door/{door_id}"
        xml_data = '''<?xml version="1.0" encoding="UTF-8"?>
<RemoteControlDoor>
    <cmd>open</cmd>
</RemoteControlDoor>'''
        headers = {'Content-Type': 'application/xml', 'Accept': 'application/xml'}
        response = self.session.put(url, data=xml_data, headers=headers, timeout=10)
        return response.status_code == 200
    
    def close_door(self, door_id=1):
        """Cierra la puerta especificada"""
        url = f"{self.base_url}/ISAPI/AccessControl/RemoteControl/door/{door_id}"
        xml_data = '''<?xml version="1.0" encoding="UTF-8"?>
<RemoteControlDoor>
    <cmd>close</cmd>
</RemoteControlDoor>'''
        headers = {'Content-Type': 'application/xml', 'Accept': 'application/xml'}
        response = self.session.put(url, data=xml_data, headers=headers, timeout=10)
        return response.status_code == 200
    
    def get_device_info(self):
        """Obtiene información del dispositivo"""
        url = f"{self.base_url}/ISAPI/System/deviceInfo"
        response = self.session.get(url, timeout=5)
        return response.text if response.status_code == 200 else None
    
    def get_events(self):
        """Obtiene eventos del dispositivo"""
        url = f"{self.base_url}/ISAPI/Event/notification/alertStream"
        response = self.session.get(url, timeout=10)
        return response.text if response.status_code == 200 else None
    
    def ptz_control(self, channel=1, action="start", speed=5):
        """Controla movimiento PTZ"""
        url = f"{self.base_url}/ISAPI/PTZCtrl/channels/{channel}/continuous"
        xml_data = f'''<?xml version="1.0" encoding="UTF-8"?>
<PTZData>
    <pan>{speed if action in ["left", "right"] else 0}</pan>
    <tilt>{speed if action in ["up", "down"] else 0}</tilt>
    <zoom>0</zoom>
</PTZData>'''
        headers = {'Content-Type': 'application/xml', 'Accept': 'application/xml'}
        response = self.session.put(url, data=xml_data, headers=headers, timeout=5)
        return response.status_code == 200

# Ejemplo de uso
if __name__ == '__main__':
    client = HikvisionISAPIClient()
    
    if client.test_connection():
        print("✅ Conectado al dispositivo")
        
        # Abrir puerta
        if client.open_door(1):
            print("✅ Puerta abierta")
            time.sleep(2)
            if client.close_door(1):
                print("✅ Puerta cerrada")
        
        # Obtener información
        info = client.get_device_info()
        if info:
            print("📱 Información del dispositivo obtenida")
    else:
        print("❌ Error de conexión")
```

## 🚀 Endpoints ISAPI Más Comunes

| Categoría | Endpoint | Descripción |
|-----------|----------|-------------|
| **Sistema** | `/ISAPI/System/deviceInfo` | Información del dispositivo |
| **Sistema** | `/ISAPI/System/status` | Estado del sistema |
| **Sistema** | `/ISAPI/System/Network/interfaces` | Configuración de red |
| **Puerta** | `/ISAPI/AccessControl/RemoteControl/door/{id}` | Control de puerta |
| **Eventos** | `/ISAPI/Event/notification/alertStream` | Stream de eventos |
| **Eventos** | `/ISAPI/Event/triggers` | Configuración de alarmas |
| **Video** | `/ISAPI/System/Video/inputs/channels` | Canales de video |
| **PTZ** | `/ISAPI/PTZCtrl/channels/{id}/continuous` | Control PTZ |
| **Audio** | `/ISAPI/System/Audio/inputs` | Configuración de audio |
| **Almacenamiento** | `/ISAPI/ContentMgmt/Storage` | Información de almacenamiento |
| **Usuarios** | `/ISAPI/Security/users` | Gestión de usuarios |

## ⚠️ Consideraciones Importantes

1. **Autenticación**: Siempre usar HTTP Digest Auth
2. **HTTPS**: Para producción, usar HTTPS con certificados válidos
3. **Timeouts**: Configurar timeouts apropiados (5-10 segundos)
4. **XML**: Los comandos de control requieren XML válido
5. **Errores**: Siempre verificar códigos de respuesta HTTP
6. **Rate Limiting**: No enviar comandos muy frecuentemente
7. **Logs**: Mantener logs de todas las operaciones

## 🔍 Códigos de Respuesta Comunes

| Código | Significado |
|--------|-------------|
| **200** | OK - Operación exitosa |
| **400** | Bad Request - XML malformado |
| **401** | Unauthorized - Credenciales incorrectas |
| **403** | Forbidden - Sin permisos |
| **404** | Not Found - Endpoint no existe |
| **500** | Internal Server Error - Error del dispositivo |

## 📚 Recursos Adicionales

- **Documentación oficial**: [Hikvision ISAPI Documentation](https://www.hikvision.com/en/support/download/technical-documents/)
- **Ejemplos XML**: Disponibles en la documentación del dispositivo
- **SDK**: Para funcionalidades avanzadas, usar el SDK oficial
- **Foros**: Comunidad Hikvision para soporte técnico

---

**¡Con esta guía puedes controlar completamente tu dispositivo Hikvision via ISAPI!** 🎉
