# flask-elastic-beanstalk

이 저장소는 한빛미디어의 <실전 MLOps> *(Noah Gift, \<Practical MLOps\>, O'Reilly)* 한국 독자들을 위해 번역과 설명이 추가된 저장소입니다. *참고: [원저자의 저장소](https://github.com/noahgift/Flask-Elastic-Beanstalk)*

- 이 저장소를 포크해 사용하기를 권장합니다.
- 포크한 뒤 `.elasticbeanstalk` 디렉터리를 제거하세요. 
- 잠시 후 `eb init` 커맨드를 사용하면 해당 디렉터리 및 하위 파일들이 다시 생겨나기 때문입니다.

## 핵심 개념

![eb-deploy-kr](https://user-images.githubusercontent.com/46595649/178249376-491d7d9e-1532-47fe-9c12-60c24b31dbd7.png)

- **AWS Elastic Beanstalk** 는 AWS 에서 애플리케이션을 배포하는 일의 대부분을 간소화하여 제공하는 고수준의 (high level) 서비스형 플랫폼 (PaaS) 입니다. **AWS EC2 인스턴스** 를 포함하여, 인스턴스 보안 그룹, 로드 밸런서, 로드 밸런서 보안 그룹, 오토스케일링 그룹, Amazon S3 버킷, Amazon CloudWatch 경보, AWS CloudFormation 스택, 도메인 이름, 이러한 모든 리소스들을 전부 Elastic Beanstalk 에서 관리합니다.
- **AWS Cloud9** 는 클라우드 IDE 로, **AWS EC2 인스턴스** 에 연결해 사용합니다.
- **Flask** 는 파이썬 기반의 오픈 소스 백엔드 프레임워크입니다. 
- **AWS Code Build** 는 [CI/CD 를 가능](https://aws.amazon.com/ko/codebuild/features/?nc=sn&loc=2)하게 하는 ['완전 관리형' 빌드 서비스](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/welcome.html)입니다. [예를 들어](https://github.com/aws-actions/aws-codebuild-run-build), [**Github Action**](https://github.com/features/actions)을 통해 코드가 변경되었을 때마다 자동적으로 클라우드에서 빌드 및 테스트 작업을 진행할 수 있도록 만드는 데 사용됩니다.
- 따라서, 이 저장소는 간단한 AWS Elastic Beanstalk 환경에 Flask 애플리케이션을 배포하는 방법을 안내합니다. Github Action 은 사용하지 않습니다.

## AWS Cloud9 + AWS Code Build 을 이용한 배포

[AWS 공식 플라스크 Elastic Beanstalk 튜토리얼](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/create-deploy-python-flask.html)을 참고해도 좋습니다. 프리티어를 사용하는 경우 과금되지 않습니다.

1. Cloud9 등을 이용해 이 저장소를 clone 하고 작업 디렉터리를 변경합니다.

```bash
git clone https://github.com/ProtossDragoon/flask-elastic-beanstalk.git
cd flask-elastic-beanstalk
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

6. `buildspec.yml` 파일을 통해 AWS Code Build 프로젝트를 세팅합니다. 이 저장소에는 이미 `buildspec.yml` 파일이 간단히 작성되어 있습니다. 파일을 설정하는 다양한 방법은 [공식 문서](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/build-spec-ref.html)에서 자세히 확인할 수 있습니다.
7. 추가적으로, [이 프로젝트](https://github.com/noahgift/flask-ml-azure-serverless)처럼 머신러닝 엔지니어링 프로젝트로 변신시키기를 진행해 봅니다.

## 기타 참고할만한 자료들

* [원저자가 O'Reilly 플랫폼에 게시해둔 Complete Walkthrough of Process](https://learning.oreilly.com/videos/aws-elastic-beanstalk/62022021VIDEOPAIML/62022021VIDEOPAIML-c1_s0)
* [Previous YouTube Walkthrough](https://youtu.be/iSv-i1tWpQc)
