**DISCLAIMER! DOKUMENTASI BELAJAR PRIDADI. DIBUAT TIDAK UNTUK UMUM!**

🌱 **Step 1: Paham Dasar Kubernetes**

✅ **Apa itu Kubernetes?** → Sistem buat **orchestrate & manage container** seperti Docker.  
✅ **Kenapa pakai Kubernetes?** → Bisa **auto-scaling, self-healing, load balancing**, dan cocok buat microservices.  
✅ **Komponen utama Kubernetes**

- **Pod** → Unit terkecil dalam Kubernetes (bisa punya satu atau lebih container).
- **Node** → Server tempat pod dijalankan.
- **Cluster** → Kumpulan node yang bekerja bersama buat jalankan aplikasi.
- **Deployment** → Cara buat nge-deploy aplikasi ke Kubernetes.
- **Service** → Layer buat **ekspos aplikasi ke jaringan atau internet**.

🚀 **Step 2: Install Kubernetes (Minikube) di Windows**

- **Install Minikube 1.33.1 (local Kubernetes)**

  Buat direktori minikube terleih dahulu di `C:/minikube`. lalu jalankan perintah berikut di powershell:

```
	Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/download/v1.33.1/minikube-windows-amd64.exe' -UseBasicParsing
```

- **Add to path**

```
	$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
	if ($oldPath.Split(';') -inotcontains 'C:\minikube'){
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine)
```

- **Jalanin minikube**

```
	minikube start
```

- **Cek status Minikube**

```
	minikube status
```

- **Stop minikube yang sedang running**

```
	minikube stop
```

- **Hapus container minikube**

```
	minikube delete --all
```

- **Masuk SSH minikube**

```
	minikube ssh
```

🚀**Optional: install choco**latey

- copy this to CMD via administrator:

```
  @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "[System.Net.ServicePointManager]::SecurityProtocol = 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

🚀**Step 3: instal kubernetes-cli (kubectl) via chocolatey**

```
	choco install kubernetes-cli --version=1.30.0 --force --allow-downgrade
```

- **Check version**

```
  kubectl version --client
```

🚀 **Step 4: Mastering Kubernetes**

✅ POD (Create pod)
Pod adalah unit terkecil di kubernetes yang didalamnya bisa terdapat lebih dari 1 container. setiap pod akan puna satu IP address.

- **Buat file yaml (namaFile.yaml)**
  contoh :

```
	apiVersion: v1

	kind: Pod

	metadata:

	  name: aplikasi-nginx

	  labels:

	    name: aplikasi-nginx

	spec:

	  containers:

	    - name: aplikasi-nginx

	      image: nginx:alpine

	      resources:

	        limits:

	          memory: "10Mi"

	          cpu: "10m"

	      ports:

		        - containerPort: 80
```

- **BASIC COMMAND POD**

  - membuat resource di kubernetes

  ```
  kubectl apply -f <file_name>
  ```

  - melihat semua pod

  ```
  kubectl get pod
  ```

  - melihat detail pod

  ```
  kubectl describe pod <pod_name>
  ```

  - melakukan port forward dari pod

  ```
  kubectl port-forward  pod/<pod_name> <port_local:port_pod>`
  ```

✅ **Kubectl exec**
command ini berguna untuk mengeksekusi perintah didalam shel kubernetes

- single container

  ```
  kubectl exec -it <pod_name> -- /bin/sh
  ```

- spesific container

  ```
  kubectl exec -it <pod_name> -c <container_name> -- /bin/sh
  ```

✅ **NODE**

- Node adalah tempat untuk menjalankan satu atau beberapa Pod.
- Kita bisa punya banyak node di Kubernetes.
- Node berupa VM atau Bare metal.
- Node juga bisa dikelompokan dengan node-pool (cloud)
- Setiap node akan punya kubelet, kube-proxy, dan container runtime

- **COMMAND NODE**

  - melihat semua node

  ```
  kubectl get node
  ```

  atau:

  ```
  kubectl get node -o wide
  ```

- melihat detail node

  ```
  kubectl describe node
  ```

✅ **Namespace**

- Namespace digunakan untuk mengelompokkan resource(seperti: backend, frontend)
- Namespace tidak mengisolasi resource
- Pod dibeda namespace tetap bisa saling berkomunikasi
- jika kita membuat namepace tanpa mendefinisikan namanya, maka akan ditaruh di namespace default

- **COMMAND**

  - membuat namespace secara langsung

  ```
  kubectl create namespace <namespace_name>
  ```

  - melihat semua namespace

  ```
  kubectl get ns
  ```

  atau

  ```
  kubectl get namespace
  ```

  - berpindah namespace

  ```
  kubectl config set-context --current --namespace <namspace_name>
  ```

- **Illustrasi** - Node-1:server_test_aplikasi - Namespace: Backend - pod-1 - pod-2 - Namespace: Frontend - pod 1
  ✅ **Label**
- digunakan untuk melabeli atau menandai sebuah resoirce seperti node, pod, namespace dan lain-lain
- formatnya adalah key-value pair

- **COMMAND**
  - mencari pod berdasarkan label
    `kubecl get pod -l key(misal: version:2.1.1)`

```
	kubecl get pod -l version:1.0.0
```

✅**Annotation**

- Sama seperti label tapi dia cuma bisa menyimpan informasi hingga 256kb
- gabisa dipake buat filter resource seperti label
- anotasi biasanya dipake buat informasi metadata

- **COMMAND**

  - melihat anotasi pada resource

  ```
  kubectl describe pod <pod_name>
  ```

  ```
  kubectl describe node <node_name>
  ```

✅**Menghapus Resource**

- kita bisa langsung hapus resource langsung dari command
- TAPI secara best practice dianjurkan menghapus resource menggunakan file .yaml resource tersebut

- **COMMAND**

  - melihat semua resource

  ```
  kubectl get all
  ```

  - menghapus resource secara langsung

  ```
  kubectl delete pod <pod_name>
  ```

  ```
  kubectl delete namespace <ns_name>
  ```

  - mengahpus menggunakan file .yaml (BEST PRACTICE)

  ```
  kubectl delete -f <file_name>
  ```

✅**ReplicaSet**

- replicaSet biasa digunakan untuk melakukan manajemen banyak pod
- replicaSet memastikan jumlah pod sesuai dengan jumlah yang ditentukan
- jika ada pod yang dihapus manual atau tidak sengaja, replicaSet akan membuat lagi pod tersebut secara otomatis berdasarkan jumlah yang ditentukan
- atau jika salah satu node sedang down, maka pod akan dibuat ulang di node lain
- Elemen yang ada di replicaSet

  - Label selector ➡️ untuk mengetahui pod mana yang harus dimanage
  - Label count ➡️ untuk mengatur jumlah pod yang berjalan
  - Pod template ➡️ untuk konfigurasi pod

- **COMMAND**

  - melihat semua replica

  ```
  kubectl get rs atau kubectl get replicasets
  ```

  - melihat detail replicatsets

  ```
  kubectl describe replicasets <name>
  ```

- **File YAML**

```
	apiVersion: apps/v1

	kind: ReplicaSet

	metadata:

	  name: myreplicaset-nginx

	  labels:

	    app: myreplicaset-nginx

	spec:

	  replicas: 3

	  selector:

	    matchLabels:

	      app: myreplicaset-nginx

	  template:

	    metadata:

	      name: myreplicaset-nginx

	      labels:

	        app: myreplicaset-nginx

	    spec:

	      - name: aplikasi-nginx

	        image: nginx:alpine

	        resources:

	        limits:

	          memory: "10Mi"

	          cpu: "10m"

	        ports:

	          - containerPort: 80
```

✅ **Deployment** - Deployment bisa mengtaur replicaset secara otomatis jika kita melakukan update atau rollback versi aplikasi - By default deployment akan menggunaka metode rolling update agar aplikasi tidak mengalami downtime - daripada harus menghapus replicaset dan menjalankan kembali, lebih baik pake deployment untuk mengabstraksi replicaset

- Ilustrasi
  - deployment akan membuat pod dengan versi ter-update
  - ![[Pasted_image_20250525005151.png]](images/Pasted_image_20250525005151.png)
  - ketika pod dengan versi ter-update berhasil dibuat, maka pod versi yang lama akan dibuang
  - ![[Pasted_image_20250525005419.png]](images/Pasted_image_20250525005419.png)
  - ![[Pasted_image_20250525005952.png]](images/Pasted_image_20250525005952.png)

✅**SERVICE**

![[Pasted_image_20250526093543.png]](images/Pasted_image_20250526093543.png)

- Untuk mengatasi masalah di atas kita bisa menggunakan Service
- Service akan menggunakan label selector untuk mengetahui pod mana yang akan di manage
- Service ini juga bisa bertindak sebagai load balancer ketika ada salah satu pod yang down
- Kubernetes punya beberapa service:

  - **ClusterIP**
  - **NodePort**
  - **LoadBalancer**

- **ClusterIP**
- ![[Pasted_image_20250526093833.png]](images/Pasted_image_20250526093833.png)
- Pada topologi diatas ClusterIP akan menjadi gerbang akses dari satu pod ke pod yang lain. misal, frontend ingin mengakses backend, daripada harus akses satu persatu pod backend yang ada, lebih baik mengakses menggunakan ClusterIP. ClusterIP akan mem-forward traffic yang dikirim oleh frontend ke setiap pod backend yang ada. cara ini jauh lebih simple dan high capability
- ![[Pasted_image_20250526094251.png]](images/Pasted_image_20250526094251.png)

- **File YAML**

```
	apiVersion: apps/v1

	kind: Deployment

	metadata:

	  name: deploy-aplikasi-nginx

	spec:

	  revisionHistoryLimit: 10

	  selector:

	    matchLabels:

	      app: deploy-aplikasi-nginx

	  template:

	    metadata:

	      labels:

	        app: deploy-aplikasi-nginx

	    spec:

	      containers:

	        - name: deploy-aplikasi-nginx

	          image: nginx:1.22

	          resources:

	            limits:

	              memory: "16Mi"

	              cpu: "16m"

	          ports:

	            - containerPort: 80

	--- # Service untuk Deployment

	apiVersion: v1

	kind: Service

	metadata:

	  name: deploy-aplikasi-nginx-service

	spec:

	  # type: ClusterIP # default

	  selector:

	    app: deploy-aplikasi-nginx-service

	  ports:

	    - port: 8082

	      targetPort: 80
```

- **NodePort**
- ![[Pasted_image_20250526143829.png]](images/Pasted_image_20250526143829.png)
- ![[Pasted_image_20250526143807.png]](images/Pasted_image_20250526143807.png)
- Berbeda dengan ClusterIP, NodePort berfungsi agar server(node) bisa diakses dari luar cluster, dalam konteks ini adalah developer/end-user
- contoh kasus: jika developer ingin mengakses sebuah server(node), maka ia perlu memanggil Ip address yang disediakan NodePort (ex: 192.68.10.1:30123), nanti dari Nodeport akan meneruskan request tersebut ke ClusterIP yang dibuat otomatis oleh kubernetes, barulah ClusterIP akan mem-forward traffic tersebut ke pod-pod yang ada didalam server tersebut.

- **LoadBalancer**
- ![[Pasted_image_20250527104632.png]](images/Pasted_image_20250527104632.png)
- ![[Pasted_image_20250527104700.png]](images/Pasted_image_20250527104700.png)
- Latar belakang: jadi misalkan kasusnya ada salah satu node atau server yang down, ketika user mengakses alamat ip dari nodePort server tersebut. maka tidak akan bisa karena down, walaupun sever yang lain masih berjalan. masalahnya user tidak tau alamat ip server yang lain. nah solusinya menggunakan load balancer sebagai layer tambahan. jadi load balancer akan otomatis mencarikan server yang running agar bisa di akses.
- simulasi di lokal: jalankan perintah `minikube tunnel` untuk menjembatani jaringan minikube lokal dengan device kita agar bisa diakses lalu jalankan perintah `kubectl apply -f deployment.yaml`
- **File YAML**:

```
	apiVersion: apps/v1

	kind: Deployment

	metadata:

	  name: deploy-aplikasi-nginx

	spec:

	  revisionHistoryLimit: 10

	  selector:

	    matchLabels:

	      app: deploy-aplikasi-nginx

	  template:

	    metadata:

	      labels:

	        app: deploy-aplikasi-nginx

	    spec:

	      containers:

	        - name: deploy-aplikasi-nginx

	          image: nginx:1.22

	          resources:

	            limits:

	              memory: "16Mi"

	              cpu: "16m"

	          ports:

	            - containerPort: 80

	--- # Service untuk Deployment

	apiVersion: v1

	kind: Service

	metadata:

	  name: deploy-aplikasi-nginx-service

	spec:

	  # type: ClusterIP # default

	  # type: NodePort

	  type: LoadBalancer

	  selector:

	    app: deploy-aplikasi-nginx-service

	  ports:

	    - port: 8082

	      targetPort: 80

	      nodePort: 30123
```

- **INGRESS**
- ![[Pasted_image_20250527111235.png]](images/Pasted_image_20250527111235.png)
- ![[Pasted_image_20250527111259.png]](images/Pasted_image_20250527111259.png)
- Latar belakang : kalo load balancer berfungsi untuk meng-expose server menggunakan IP publik (jika dilokal IP lokal 127.0.0.1). **Ingress** ini bisa berfungsi meng-expose server ke HTTP/HTTPS menggunakan domain path seperti `my-application.info`

- **File YAML**

```
	apiVersion: apps/v1

	kind: Deployment

	metadata:

	  name: deploy-aplikasi-nginx

	spec:

	  revisionHistoryLimit: 10

	  selector:

	    matchLabels:

	      app: deploy-aplikasi-nginx

	  template:

	    metadata:

	      labels:

	        app: deploy-aplikasi-nginx

	    spec:

	      containers:

	        - name: deploy-aplikasi-nginx

	          image: nginx:1.22

	          resources:

	            limits:

	              memory: "16Mi"

	              cpu: "16m"

	          ports:

	            - containerPort: 80



	--- # Service untuk Deployment

	apiVersion: v1

	kind: Service

	metadata:

	  name: deploy-aplikasi-nginx-service

	spec:

	  type: ClusterIP # default

	  # type: NodePort

	  # type: LoadBalancer

	  selector:

	    app: deploy-aplikasi-nginx

	  ports:

	    - port: 8082

	      targetPort: 80

	      # nodePort: 30123



	--- # Ingress untuk Deployment

	apiVersion: networking.k8s.io/v1

	kind: Ingress

	metadata:

	  name: ingress-aplikasi-nginx

	  labels:

	    name: ingress-aplikasi-nginx

	spec:

	  rules:

	    - host: my-application.info # domain path

	      http:

	        paths:

	          - pathType: Prefix

	            path: "/"

	            backend:

	              service:

	                name: deploy-aplikasi-nginx-service # nama service

	                port:

	                  number: 8082 # port service
```

                     

✅**DaemonSet**

- ![[Pasted_image_20250528113354.png]](images/Pasted_image_20250528113354.png)
- Daemon sets ini biasanya digunakan untuk menjalankan tugas tertentu di server seperti untuk monitoring server, jadi dia akan mengambil data-data seperi log, cpu dan memory usage untuk kebutuhan monitoring dan logging.
- daemon sets ini akan selalu running ketika pod tersebut running.

✅**Cron jobs**

- ![[Pasted_image_20250528114924.png]](images/Pasted_image_20250528114924.png)
- cron jobs berfungsi untuk menjalankan tugas terjadwal pada waktu tertentu, yang mana tugas ini hanya berjalan di waktu tertentu kemudian berhenti jika tugasnya selesai
- cron jobs biasanya dipakai untuk tugas-tugas seperti backup database, report, dan pembersihan cache/data
- sebagai contoh berikut konfigurasi cronjobs untuk menampilkan output sederhana selama setiap satu menit:
- **Command**

```
	apiVersion: batch/v1

	kind: CronJob

	metadata:

	  name: hello

	spec:

	  schedule: "* * * * *"

	  jobTemplate:

	    spec:

	      template:

	        spec:

	          containers:

	            - name: hello

	              image: busybox:1.28

	              # imagePullPolicy: IfNotPresent

	              command:

	                - /bin/sh

	                - -c

	                - date; echo Hello from the Kubernetes cluster

	          restartPolicy: OnFailure
```

- untuk menjalankannya bisa menggunakan perintah `kubectl apply -f cronjobs.yaml`
- lalu jalankan perintah `kubectl get cronjobs.batch` untuk menampilkan cronjobs yang sedang berjalan.
- ![[Pasted_image_20250528155448.png]](images/Pasted_image_20250528155448.png)
- jika sudah satu menit, maka status activenya akan berubah
  ![[Pasted_image_20250528155534.png]](images/Pasted_image_20250528155534.png)
- jika active-nya berubah, maka harusnya tugas tersebut statusnya akan menjadi complete. bisa di cek menggunakan comman `kubectl get pod`
- ![[Pasted_image_20250528155751.png]](images/Pasted_image_20250528155751.png)
- kita juga bisa mengecek logs-nya menggunakan perintah `kubectl logs -f <nama_pod>`
- ![[Pasted_image_20250528155852.png]](images/Pasted_image_20250528155852.png)

✅**Environtment Variable**
![[Pasted_image_20250528160240.png]](images/Pasted_image_20250528160240.png)

- Fungsi dari env ini adalah untuk membuat env manual pada pod kita
- nah untuk mengecek env apa yang terdaftar pada pod kita bisa menggunakan command `kubectl exec -it <pod> -- env` atau cek env-nya melalui shell

- **COMMAND**

```
	apiVersion: apps/v1

	kind: Deployment

	metadata:

	  name: deploy-aplikasi-nginx

	spec:

	  revisionHistoryLimit: 10

	  selector:

	    matchLabels:

	      app: deploy-aplikasi-nginx

	  template:

	    metadata:

	      labels:

	        app: deploy-aplikasi-nginx

	    spec:

	      containers:

	        - name: deploy-aplikasi-nginx

	          image: nginx:1.22

	          resources:

	            limits:

	              memory: "16Mi"

	              cpu: "16m"

	          ports:

	            - containerPort: 80

	          env: # Environment variables

	            - name: DB_PORT

	              value: "5432"

	            - name: DB_USERNAME

	              value: "danu"
```

✅**Secret**

- ![[Pasted_image_20250528160944.png]](images/Pasted_image_20250528160944.png)
- **COMMAND**
  - membuat secret

```
		kubectl create secret generic my-secret --from-literal=DB_PASSWORD=12345 --from-literal=DB_NAME=my_db
```

- melihat daftar secret

```
		kubectl get secret
```

- melihat detail secret

```
		kubectl describe secret <secret_name>
```

- menampilkan detail secret dalam output YAML

```
		kubectl get secret <secret_name> -o yaml
```

- untuk mendecode secret yang di ekripsi menggunakan base64 di git

```
		echo -n "dataYangDiEnkripsi" | base64 -d
```

![[Pasted_image_20250609144157.png]](images/Pasted_image_20250609144157.png)

- **File YAML**

```
	apiVersion: apps/v1

	kind: Deployment

	metadata:

	  name: deploy-aplikasi-nginx

	spec:

	  revisionHistoryLimit: 10

	  selector:

	    matchLabels:

	      app: deploy-aplikasi-nginx

	  template:

	    metadata:

	      labels:

	        app: deploy-aplikasi-nginx

	    spec:

	      containers:

	        - name: deploy-aplikasi-nginx

	          image: nginx:1.22

	          resources:

	            limits:

	              memory: "16Mi"

	              cpu: "16m"

	          ports:

	            - containerPort: 80

	          env: # Environment variables

	            - name: DB_PORT

	              value: "5432"

	            - name: DB_USERNAME

	              value: "danu"

	            - name: DB_PASSWORD

	              valueFrom:

	                secretKeyRef:

	                  name: my-secret # nama secret

	                  key: DB_PASSWORD # key pada secret
```

- menerapkan perubahan

```
	kubectl apply -f filename.yaml
```

- cek apakah pod statusnya sudah running

```
	kubectl get pod
```

![[Pasted_image_20250609145115.png]](images/Pasted_image_20250609145115.png)

- cek environtment yang ada didalam pod

```
	kubectl exec -it <pod_name> -- env
```

![[Pasted_image_20250609145200.png]](images/Pasted_image_20250609145200.png)

✅**Secret: Mount File**
![[Pasted_image_20250610092956.png]](images/Pasted_image_20250610092956.png)

- Contoh kasus: misal kita punya file dengan nama private-key.txt, kita bisa memasukkan file ini kedalam secret dengan cara seperti ini:
- buat secret

```
	kubectl create secret generic my-private-key --from-file=private-key.txt
```

- cek secret

```
	kubectl get secret
```

- buat file yaml

```
	apiVersion: apps/v1

		kind: Deployment

		metadata:

		  name: deploy-aplikasi-nginx

		spec:

		  revisionHistoryLimit: 10

		  selector:

		    matchLabels:

		      app: deploy-aplikasi-nginx

		  template:

		    metadata:

		      labels:

		        app: deploy-aplikasi-nginx

		    spec:

		      containers:

		        - name: deploy-aplikasi-nginx

		          image: nginx:1.22

		          resources:

		            limits:

		              memory: "16Mi"

		              cpu: "16m"

		          ports:

		            - containerPort: 80

		          env: # Environment variables

		            - name: DB_PORT

		              value: "5432"

		            - name: DB_USERNAME

		              value: "danu"

		            - name: DB_PASSWORD

		              valueFrom:

		                secretKeyRef:

		                  name: my-secret # nama secret

		                  key: DB_PASSWORD # key pada secret

		          volumeMounts: # Mount file secret ke dalam container

		            - name: private-key

		              mountPath: /var/secret/private_key # path mount pada container

		              readOnly: true

		      volumes:

		        - name: private-key

		          secret: # Menggunakan secret sebagai volume

		            secretName: my-private-key # nama secret yang sudah dibuat
```

- apply

```
	kubectl apply -f nama_file.yaml
```

    - cek file secret yang sudah di mount ke dalam container
    	- masuk ke terminal

```
			kubectl exec it pod_name -- /bin/sh
```

    	- cek direktori untuk mencari direktori secret

```
			ls
```

    	- masuk ke dalam direktori secret

```
			cd var/secret/private_key
```

✅**ConfigMaps**
![[Pasted_image_20250610154153.png]](images/Pasted_image_20250610154153.png)

- Contoh kasus: ada aplikasi yang berjalan di Kubernetes dan mau konfigurasi seperti **URL API**, **APP NAME**, dan **mode debug** dikelola/kustomisasi tanpa harus mengubah kode aplikasi.
- ⚙️ **Konfigurasi file yaml**

```
	apiVersion: apps/v1

	kind: Deployment

	metadata:

	  name: deploy-aplikasi-nginx

	spec:

	  revisionHistoryLimit: 10

	  selector:

	    matchLabels:

	      app: deploy-aplikasi-nginx

	  template:

	    metadata:

	      labels:

	        app: deploy-aplikasi-nginx

	    spec:

	      containers:

	        - name: deploy-aplikasi-nginx

	          image: nginx:1.22

	          resources:

	            limits:

	              memory: "16Mi"

	              cpu: "16m"

	          ports:

	            - containerPort: 80

	          env: # Environment variables

	            - name: DB_PORT

	              value: "5432"

	            - name: DB_USERNAME

	              value: "danu"

	            - name: DB_PASSWORD

	              valueFrom:

	                secretKeyRef:

	                  name: my-secret # nama secret

	                  key: DB_PASSWORD # key pada secret

	            - name: API_URL

	              valueFrom:

	                configMapKeyRef:

	                  name: app-settings # nama config map

	                  key: API_URL # key pada config map

	            - name: APP_NAME

	              valueFrom:

	                configMapKeyRef:

	                  name: app-settings

	                  key: APP_NAME

	            - name: DEBUG_MODE

	              valueFrom:

	                configMapKeyRef:

	                  name: app-settings

	                  key: debug_mode

	          volumeMounts: # Mount file secret ke dalam container

	            - name: private-key

	              mountPath: /var/secret/private_key # path mount pada container

	              readOnly: true

	      volumes:

	        - name: private-key

	          secret: # Menggunakan secret sebagai volume

	            secretName: my-private-key # nama secret yang sudah dibuat



	--- # config map

	apiVersion: v1

	kind: ConfigMap

	metadata:

	  name: app-settings

	data:

	  API_URL: "https://api.example.com"

	  APP_NAME: "My Application"

	  debug_mode: "true"
```

- Apply konfigurasi

```
	kubectl apply -f nama_file.yaml
```

- Cek apakah konfigurasi berhasil tersimpan ke ENV

```
	kubectl describe pod pod_name
```

    	atau via terminal SSH

```
	kubectl exec -it pod_name -- /bin/sh
```

- Edit configMap

```
	kubectl edit configmap app-settings
```

**💡 Catatan Penting**

- Jika ConfigMap digunakan sebagai **environment variable**, perubahan tidak akan langsung diterapkan ke Pod yang sedang berjalan. Kamu perlu melakukan **rolling restart** pada Deployment:

```
kubectl rollout restart deployment pod_name
```

- Jika ConfigMap digunakan sebagai **volume**, perubahan akan langsung terlihat tanpa perlu restart Pod.

✅**Autoscaler**
Autoscaler di Kubernetes adalah mekanisme yang secara otomatis menyesuaikan jumlah sumber daya komputasi (CPU, memori, atau jumlah Pod) yang dialokasikan untuk aplikasi atau klaster, berdasarkan beban kerja atau metrik yang ditentukan. Tujuannya untuk mengoptimalkan pemanfaatan sumber daya, menjaga kinerja aplikasi, dan efisiensi biaya.
Ada 3 jenis autoscaler yaitu Horizontal Pod Autoscaler, Vertical Pod Autoscaler, dan Cluster/Node Autoscaler.
![[Pasted_image_20250612092513.png]](images/Pasted_image_20250612092513.png)

- Horizontal Pod Autoscaler
  HPA akan memantau metrik yang ditentukan. Jika metrik tersebut melebihi ambang batas yang dikonfigurasi, HPA akan menambah/mereplika jumlah Pod. Sebaliknya, jika metrik turun di bawah ambang batas, HPA akan mengurangi jumlah Pod.
  ![[Pasted_image_20250612092600.png]](images/Pasted_image_20250612092600.png)

  ![[Pasted_image_20250612092623.png]](images/Pasted_image_20250612092623.png)

  ⚙️ **How to configure**

  - Cek installasi addons `metrics-server`

  ```
  	minikube addons list
  ```

  - Aktifkan addons `metrics-server`

  ```
  	minikube addons enable metrics-server
  ```

  📝 **Buat file YAML**

```
	apiVersion: apps/v1

	kind: Deployment

	metadata:

	  name: deploy-aplikasi-nginx

	spec:

	  revisionHistoryLimit: 10

	  selector:

	    matchLabels:

	      app: deploy-aplikasi-nginx

	  template:

	    metadata:

	      labels:

	        app: deploy-aplikasi-nginx

	    spec:

	      containers:

	        - name: deploy-aplikasi-nginx

	          image: nginx:1.22

	          resources:

	            limits:

	              memory: "16Mi"

	              cpu: "16m"

	          ports:

	            - containerPort: 80

	          env: # Environment variables

	            - name: DB_PORT

	              value: "5432"

	            - name: DB_USERNAME

	              value: "danu"

	            - name: DB_PASSWORD

	              valueFrom:

	                secretKeyRef:

	                  name: my-secret # nama secret

	                  key: DB_PASSWORD # key pada secret

	            - name: API_URL

	              valueFrom:

	                configMapKeyRef:

	                  name: app-settings # nama config map

	                  key: API_URL # key pada config map

	            - name: APP_NAME

	              valueFrom:

	                configMapKeyRef:

	                  name: app-settings

	                  key: APP_NAME

	            - name: DEBUG_MODE

	              valueFrom:

	                configMapKeyRef:

	                  name: app-settings

	                  key: debug_mode

	          volumeMounts: # Mount file secret ke dalam container

	            - name: private-key

	              mountPath: /var/secret/private_key # path mount pada container

	              readOnly: true

	      volumes:

	        - name: private-key

	          secret: # Menggunakan secret sebagai volume

	            secretName: my-private-key # nama secret yang sudah dibuat



	--- # Horizontal Pod Autoscaler

	apiVersion: autoscaling/v2

	kind: HorizontalPodAutoscaler

	metadata:

	  name: hpa-deploy-aplikasi-nginx

	spec:

	  scaleTargetRef:

	    apiVersion: apps/v1

	    kind: Deployment

	    name: deploy-aplikasi-nginx

	  minReplicas: 1 # jumlah minimum pod yang direplika

	  maxReplicas: 5 # jumlah maksimum pod yang direplika

	  metrics:

	    - type: Resource # jenis metrik yang digunakan

	      resource:

	        name: cpu # nama resource yang dimonitor

	        target:

	          type: Utilization

	          averageUtilization: 50 # persentase rata-rata CPU

	    - type: Resource

	      resource:

	        name: memory

	        target:

	          type: Utilization

	          averageUtilization: 50 # persentase rata-rata memori
```

- **Apply konfigurasi**

```
	kubectl apply -f nama_file
```

- Cek List Pod (Sebelum HPA ditrigger)
  ```
  kubectl get pod
  ```
  ![[Pasted_image_20250612104037.png]](images/Pasted_image_20250612104037.png)
- Cek apakah HPA berhasil dibuat

  ```
  kubectl get hpa
  ```

  ![[Pasted_image_20250612104107.png]](images/Pasted_image_20250612104107.png)

- Trigger HPA
  - Menggunakan port forward untuk mengexpose servicenya
    - cek service yang berjalan
      ```
      kubectl get service
      ```
      ![[Pasted_image_20250612110053.png]](images/Pasted_image_20250612110053.png)
    - port forward
      ```
      kubectl  port-forward svc/nama_service port:port
      ```
      ![[Pasted_image_20250612110230.png]](images/Pasted_image_20250612110230.png)
  - Menggunakan apache benchmark
    Untuk mengaktifkan fitur ini teman-teman perlu mengaktifkan apache service melalui XAMPP
    ab -c 100 -n 3000 -t 60 http://127.0.0.1:8082/
- Cek Beban kerja pod
  ![[Pasted_image_20250612112410.png]](images/Pasted_image_20250612112410.png)
- Watch pod (pantau pod)
  Gunakan command watch di WSL untuk mengecek pod yang aktif

  ```
  watch kubectl get pod
  ```

  Jika HPA ketrigger, maka pod akan tereplika secara otomatis

  Sekian!
