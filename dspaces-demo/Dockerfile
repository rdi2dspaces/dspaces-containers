FROM philipdavis/dspaces-jupyter:latest

RUN git clone https://github.com/philip-davis/pydsvdc.git
RUN pip3 install pydsvdc/
RUN pip3 install boto3
RUN apt install -y net-tools iputils-ping dnsutils
