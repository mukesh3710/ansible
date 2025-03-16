## YAML

YAML (Yet Another Markup Language) is widely used in Kubernetes/OpenShift for defining configurations such as Pods, Deployments, Services, ConfigMaps, and more. Here’s a quick guide to YAML basics and its role in Kubernetes/OpenShift.

---
### Key-Value Pairs
- YAML uses key-value pairs for defining objects. Format: key: value
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
```
---
### Indentation Matters (Use Spaces, Not Tabs)
- YAML uses spaces (not tabs) for indentation. ncorrect indentation breaks parsing.
```yaml
metadata:
  name: my-app
```

---
### Lists (Arrays)
- Lists are defined using - (hyphen).
```yaml
spec:
  containers:
    - name: nginx
      image: nginx:latest
    - name: redis
      image: redis:latest
```
---
### Nested Structures (Hierarchy)
- Nested elements are defined using indentation.
```yaml
metadata:
  labels:
    app: my-app
    tier: backend
```
---
### Strings: Quoted vs. Unquoted
- Strings can be quoted ("" or '') or unquoted.
- Use quotes when:
  - Strings contain special characters (: or @).
  - Strings start with numbers (to prevent misinterpretation).
```yaml
appVersion: "1.0"
description: 'This is a sample app'
```
---
### Multi-Line Strings
- Use | (pipe) for preserving line breaks.
- Use > (greater-than) for collapsing lines into one.
```yaml
description: |
  This is a multi-line
  string example.

description: >
  This is a multi-line
  string, but it is collapsed
  into a single line.
```
### Comments
- Comments start with # and are ignored by YAML.
```yaml
# This is a comment
kind: Pod
```
---
### Boolean Values
- true, false, yes, no, on, off are valid boolean values.
```yaml
isEnabled: true
debugMode: false
```
---
### Numeric Values
- Integers: 100, Floats: 3.14
```yaml
replicas: 3
cpuLimit: 0.5
```
---
### Anchors & Aliases (Reusing Data)
- Use & to define an anchor (template).
- Use * to reference an anchor.
```yaml
defaults: &default-settings
  image: nginx:latest
  ports:
    - containerPort: 80

spec:
  containers:
    - <<: *default-settings
      name: nginx-container
```
