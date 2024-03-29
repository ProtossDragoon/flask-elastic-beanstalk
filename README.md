# flask-elastic-beanstalk

- [원저자 소스코드](https://github.com/noahgift/Flask-Elastic-Beanstalk)
- [『MLOps 실전 가이드』 소스코드 길라잡이](https://github.com/ProtossDragoon/practical-mlops)

## 핵심 개념

![eb-deploy-kr](https://user-images.githubusercontent.com/46595649/218323591-4c5583d3-83aa-4ac6-a488-9f3686738b4f.png)

- **AWS Elastic Beanstalk** 는 AWS 에서 애플리케이션을 배포하는 일의 대부분을 간소화하여 제공하는 고수준의 (high level) 서비스형 플랫폼 (PaaS) 입니다. **AWS EC2 인스턴스** 를 포함하여, 인스턴스 보안 그룹, 로드 밸런서, 로드 밸런서 보안 그룹, 오토스케일링 그룹, Amazon S3 버킷, Amazon CloudWatch 경보, AWS CloudFormation 스택, 도메인 이름, 이러한 모든 리소스들을 전부 Elastic Beanstalk 에서 관리합니다.
- **AWS Cloud9** 는 클라우드 IDE 로, **AWS EC2 인스턴스** 에 연결해 사용합니다.
- **Flask** 는 파이썬 기반의 오픈 소스 백엔드 프레임워크입니다. 
- **AWS Code Build** 는 [CI/CD 를 가능](https://aws.amazon.com/ko/codebuild/features/?nc=sn&loc=2)하게 하는 ['완전 관리형' 빌드 서비스](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/welcome.html)입니다.

## AWS Code Build 와 AWS Elastic Beanstalk 을 이용한 배포

[AWS 공식 플라스크 Elastic Beanstalk 튜토리얼](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/create-deploy-python-flask.html)을 참고해도 좋습니다. 프리티어를 사용하는 경우 과금되지 않습니다.

1. Cloud9 등을 이용해 이 저장소를 clone 하고 작업 디렉터리를 변경합니다. 

**NOTE**: 포크한 뒤 `.elasticbeanstalk` 디렉터리를 제거하세요. 잠시 후 `eb init` 커맨드를 사용하면 해당 디렉터리 및 하위 파일들이 다시 생겨나기 때문입니다.

```bash
git clone https://github.com/ProtossDragoon/flask-elastic-beanstalk.git
cd flask-elastic-beanstalk
rm -r .elasticbeanstalk
```

2. python 가상 환경(venv 사용)을 생성하고 소싱(sourcing)합니다.
3. `make all` 합니다.

```bash
python3 -m venv ~/.eb
source ~/.eb/bin/activate
make all
```

**NOTE**: `awsebcli` 는 `requirements.txt` 를 통해 설치됩니다. [AWS Elastic Beanstalk CLI 설치 공식 문서](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-install-advanced.html)를 통해 관련 내용을 확인할 수 있습니다. <br>
**NOTE**: `make all` 을 실행하는 경우에는 `make lint`, `make test` 가 실행됩니다. 이들은 당연히 모두 `Makefile` 에 정의되어 있습니다.

4. 새로운 Elastic Beanstalk 애플리케이션을 초기화합니다.

```bash
eb init -p python-3.7 flask-continuous-delivery --region us-east-1
```

**NOTE**: SSH를 통해 애플리케이션을 실행하는 EC2 인스턴스에 연결하고 싶은 경우에만 ssh key  생성을 위해 `eb init` 을 한번 더 실행하세요. <br>
**NOTE**: `flask-continuous-delivery` 대신 임의의 애플리케이션 이름을 사용한다면, `Makefile` 파일의 `eb deploy` 뒤 내용을 변경해 주는 것을 잊지 마세요. (잠시 후에도 확인하겠지만, `buildspec.yml` 파일이 make 명령어를 실행합니다. make 명령어에 대해서는 `Makefile` 에 명세되어 있습니다. `eb deploy` 명령어에 대한 자세한 내용은 [공식 문서](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb3-deploy.html)를 참고하세요)

5. 리모트 Elastic Beanstalk 인스턴스를 생성합니다.

```bash
eb create flask-continuous-delivery-env
```

**NOTE**: 만약 Elastic Beanstalk 인스턴스 생성이 처음이라면, [이슈](https://github.com/aws/aws-elastic-beanstalk-cli/issues/26#issuecomment-791604066)에 따라 정책을 부여해야 합니다. AWS Identity and Access Management(IAM) 의 [정책 페이지](https://us-east-1.console.aws.amazon.com/iamv2/home#/policies)에서 [역할을 생성할 수 있는 정책(CreateRole)](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_roles_create_for-service.html)을 추가하고 [역할 페이지](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/roles)에서 

6. `buildspec.yml` 파일을 통해 AWS Code Build 프로젝트를 세팅합니다. 이 저장소에는 이미 `buildspec.yml` 파일이 간단히 작성되어 있습니다. 파일을 설정하는 다양한 방법은 [공식 문서](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/build-spec-ref.html)에서 자세히 확인할 수 있습니다.
7. [4장에서 실습했던 내용](https://github.com/ProtossDragoon/flask-docker-onnx-azure)처럼 머신러닝 엔지니어링 프로젝트로 변신시키기를 진행해 봅니다.

## 기타 참고할만한 자료들

* [원저자가 O'Reilly 플랫폼에 게시해둔 Complete Walkthrough of Process](https://learning.oreilly.com/videos/aws-elastic-beanstalk/62022021VIDEOPAIML/62022021VIDEOPAIML-c1_s0)
* [Previous YouTube Walkthrough](https://youtu.be/iSv-i1tWpQc)
