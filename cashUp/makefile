docker-user=akki1999

image=${docker-user}/productservice

containerID := $(shell docker ps -a -q --filter ancestor=${image})

k8sDeployment=k8s-deployment.yaml
k8sService=k8s-service.yaml
k8sConfigMap=k8s-config-map.yaml
k8sSecrets=k8s-secrets.yaml
k8sRole=k8s-role.yaml


#===================  maven  ===========================
clean:
	./mvnw clean
build: clean
	./mvnw  install
run-local: build
	java -jar target/*.jar
# #===================  gradle  ===========================
# clean:
# 	./gradlew clean
# build: clean
# 	./gradlew  bootJar
# run-local: build
# 	java -jar build/libs/ServiceA-*.jar

#===================  docker  ===========================
docker-build: build
	docker build -t ${image}:latest .


docker-run: docker-build
	docker run -p 8080:8080 ${image}


docker-login:
	docker login

docker-push: docker-login docker-build
	docker push ${image}

docker-bash:
	docker exec -ti ${image} /bin/sh

docker-containerID:
	@echo running: $(containerID)

docker-stop:
	docker stop $(containerID)

docker-remove:
	docker rm $(containerID)

#remove all unused images
docker-prune:
	docker image prune -a -f


#===================  minikube  ===========================

#Use Docker of your host to communicate with the Docker daemon inside the Minikube VM:
k8s-use-minikube-docker:
	@eval $$(minikube docker-env) 
	echo "$$(minikube docker-env)"

k8s-deployment-create: k8s-use-minikube-docker  docker-push
	kubectl apply -f ${k8sDeployment}

k8s-deployment-replace:
	kubectl replace -f ${k8sDeployment}
k8s-deployment-delete:
	kubectl delete -f ${k8sDeployment}


k8s-service-create:
	kubectl apply -f ${k8sService}
k8s-service-delete:
	kubectl delete -f ${k8sService}

k8s-service-url:
	$(eval k8s-service-name=$(shell sh -c "kubectl get -f ${k8sService} | grep -v NAME" | awk '{print $$1}'))
	minikube service ${k8s-service-name} --url


k8s-configmap-create:
	kubectl apply  -f ${k8sConfigMap}
k8s-configmap-delete:
	kubectl delete -f ${k8sConfigMap}


k8s-secrets-create:
	kubectl apply  -f ${k8sSecrets}
k8s-secrets-delete:
	kubectl delete -f ${k8sSecrets}

#define access for default service account on K8s to grant list resources
k8s-role-create:
	kubectl apply -f ${k8sRole}
k8s-role-delete:
	kubectl delete -f ${k8sRole}

k8s-all-create: k8s-configmap-create k8s-secrets-create k8s-role-create k8s-deployment-create k8s-service-create
	echo "Done"
k8s-all-delete: k8s-configmap-delete k8s-secrets-delete k8s-deployment-delete k8s-service-delete
	echo "Done"


k8s-all-reset: k8s-all-delete k8s-all-create









