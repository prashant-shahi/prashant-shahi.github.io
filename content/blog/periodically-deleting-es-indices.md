---
title: "Deleting ElasticSearch indices periodically using Curator and Cron Job"
date: 2019-04-10T08:30:00+05:30
author: Prashant Shahi
show_reading_time: true
toc: true
tags:
  - elasticsearch
  - curator
  - cronjob
---

![ELK Curator Image](/images/elk-curator.jpg)

Here is a complete step-by-step instruction to set a cronjob using Curator to delete ElasticSearch indices periodically.

## Installation of Python and Pip

You can skip this step, if you already have `python` and `pip`.

**Step 1:**

Ensure that all packages are up-to-date. After running the command below, you’ll get an output of any packages getting their update.

```sh
sudo apt-get update
```

**Step 2:**

Install `python` and `pip` using *apt-get*.

```sh
sudo apt-get install -y python-dev python-pip
```

**Step 3:**

Verifying the installation of pip:

```sh
pip — version
```

**Output:**

```sh
pip 8.1.1 from /usr/lib/python2.7/dist-packages (python 2.7)
```

## Installation of Curator

Using `pip`, installation of `elasticsearch-curator` can be done :

```sh
pip install elasticsearch-curator
```

Upon running `curator`, you might get the following warning for deprecated loader:

```
YAMLLoadWarning: calling yaml.load() without Loader=... is deprecated, as the default Loader is unsafe. Please read https://msg.pyyaml.org/load for full details.
```

Though this warning shouldn’t affect any of the operations, you can hide this warning with two methods:

**Method 1 :**

Hiding the warning, by adding ignore rule to the `PYTHONWARNINGS` environment variable.

```sh
export PYTHONWARNINGS=ignore::yaml.YAMLLoadWarning
```

You can read more about PYTHONWARNINGS [here](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONWARNINGS).

**Method 2 :**

Passing `Loader=yaml.FullLoader` as second parameter for `yaml.load()` function.
Open utils.py from the installed curator python package:

```sh
vim ~/.local/lib/python2.7/site-packages/curator/utils.py
```

Go to line with `return yaml.load(read_file(path))`, in my case, I found it in line number *53*.
Replace that line with `return yaml.load(read_file(path), Loader=yaml.FullLoader)`.


## Running Curator

For the purpose of running curator, we need to two yaml files, client yaml curator.yml, and action yaml delete-indices.yml.

Let’s execute the following command, which deletes all the ElasticSearch indices with efk- prefix, and are older than 3 days.

```sh
curator delete-indices.yml — config curator.yml
```

Now, you should see output like this :

```python
2019-04-10 10:06:25,279 INFO      Preparing Action ID: 1, "delete_indices"
2019-04-10 10:06:25,289 INFO      Trying Action ID: 1, "delete_indices": Delete indices older than 3 days (based on index name), for efk- prefixed indices. Ignore the error if the filter does not result in an actionable list of indices (ignore_empty_list) and exit cleanly.
2019-04-10 10:06:25,388 INFO      Deleting selected indices: [u'efk-2019.04.07', u'efk-2019.04.06']
2019-04-10 10:06:25,388 INFO      ---deleting index efk-2019.04.07
2019-04-10 10:06:25,388 INFO      ---deleting index efk-2019.04.06
```

Note: You can pass `--dry-run` flag with the curator to test the output of the action, without actually performing the action in ElasticSearch.

## Setting up Cron Job

We can schedule the ElasticSearch indices deletion with curator in a cron.

```sh
crontab -e
```

Add the following line in the bottom of the crontab file. Verify the exact path of the yaml files before you add it.

```
00 6 * * * root curator /path/to/delete-indices.yml — config /path/to/curator.yml
```

This configuration will clean up the indices older than 3 days every day at 6 AM.

---

This article was originally published in https://medium.com/@coolboi567/deleting-elasticsearch-indices-periodically-using-curator-and-cron-job-41e7931308cc/.
