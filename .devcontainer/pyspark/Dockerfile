# https://learn.microsoft.com/ja-jp/azure/synapse-analytics/spark/apache-spark-32-runtime
ARG spark_version=3.2.1
ARG jdk_version=8
ARG hadoop_version=3.3.1
ARG python_version=3.8

FROM python:${python_version}-slim-buster AS py
FROM openjdk:${jdk_version}-slim-buster
COPY --from=py / /

ARG spark_version
ARG hadoop_version
ENV HADOOP_HOME=/usr/local/spark/bin
ENV LD_LIBRARY_PATH=/usr/local/spark/lib/native
ENV PYSPARK_PYTHON=/usr/local/bin/python3

WORKDIR /build
ADD requirements.txt .

RUN <<EOF
	apt-get update
	apt-get install -y --no-install-recommends git wget

	wget https://dlcdn.apache.org/hadoop/common/hadoop-${hadoop_version}/hadoop-${hadoop_version}.tar.gz
	tar zxf hadoop-${hadoop_version}.tar.gz
	mv hadoop-${hadoop_version} /usr/local/spark
	rm hadoop-${hadoop_version}.tar.gz

	# install python requirements
	python -m pip install --no-cache-dir --upgrade pip
	pip install --no-cache-dir pyspark==${spark_version}
	pip install --no-cache-dir -r requirements.txt

	# clean up
	apt-get autoremove -y # インストール後不要になった依存パッケージの削除
	apt-get clean # キャッシュされたデータの削除
	rm -rf /var/lib/apt/lists/* # apt-get updateで取得したパッケージのリストを削除
EOF