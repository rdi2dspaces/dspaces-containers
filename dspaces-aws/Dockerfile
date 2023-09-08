FROM philipdavis/dspaces:bionic-margo-plus

RUN mkdir /dspaces

RUN  mkdir /install \
  && mkdir /install/dspaces

RUN touch dataspaces.conf

RUN apt-get update

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y curl awscli jq

CMD . /etc/profile.d/z10_spack_environment.sh \
  && cd /dspaces \
  && git clone https://github.com/rdi2dspaces/dspaces -b aws \
  && mkdir /dspaces/dspaces/build \
  && cd - \
  && aws ecs list-tasks --cluster dspaces-test --region us-east-2 \
  && echo "ECS_CONTAINER_METADATA_URI = ${ECS_CONTAINER_METADATA_URI}" \
  && export DSPACES_PRIVATE_IP=$(curl ${ECS_CONTAINER_METADATA_URI} | jq -r .Networks[0].IPv4Addresses[0]) \
  && export AWS_ARN_ID=$(curl ${ECS_CONTAINER_METADATA_URI} | jq .Labels | jq -r '."com.amazonaws.ecs.task-arn"' | sed 's/^.*\///') \
  && export AWS_ENI_ID=$(aws ecs describe-tasks --cluster dspaces-test --region us-east-2 --tasks ${AWS_ARN_ID} | jq -r '.tasks[0].attachments[0].details' | grep -A 1 networkInterfaceId | sed -n '/value/ s/^.*\"\(.*\)\"$/\1/p') \
  && export DSPACES_PUBLIC_IP=$(aws ec2 describe-network-interfaces --network-interface-ids ${AWS_ENI_ID} | jq -r .NetworkInterfaces[0].PrivateIpAddresses[0].Association.PublicIp) \
  && echo "DSPACES_PRIVATE_IP = ${DSPACES_PRIVATE_IP}" \
  && echo "AWS_ARN_ID = ${AWS_ARN_ID}" \
  && echo "AWS_ENI_ID = ${AWS_ENI_ID}" \
  && echo "DSPACES_PUBLIC_IP = ${DSPACES_PUBLIC_IP}" \
  && /dspaces/dspaces/scripts/build-and-run.sh ${REPO_BRANCH} /dspaces/dspaces /dspaces/dspaces/build /install/dspaces mpicc mpifort ${OFI_STRING}
