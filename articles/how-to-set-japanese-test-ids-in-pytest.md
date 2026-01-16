---
title: "pytest のパラメーター化テストに日本語の ID を設定する方法"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "pytest", "初心者"]
published: false
published_at: 2025-12-28 19:03
---

## はじめに

この記事では pytest のパラメーター化テストで ID を指定する方法と、その ID に日本語を使うための設定について説明します。

## ID を設定する方法

pytest のパラメーター化テストに ID を指定する方法は2つあります。

* pytest.mark.parametrize の ids を利用する
* pytest.param の id を利用する

```python
import pytest

@pytest.mark.parametrize("num", [1, 2, 3], ids=["test1", "test2", "test3"])
def test_parametrize_ids(num):
    assert isinstance(num, int)


@pytest.mark.parametrize(
    "num",
    [
        pytest.param(1, id="test1"),
        pytest.param(2, id="test2"),
        pytest.param(3, id="test3"),
    ],
)
def test_param_id(num):
    assert isinstance(num, int)
```

テストを実行する際に -v オプションを指定すると実行結果に ID が表示されるようになります。 

```log
$ pytest -v sample.py
==================================================================== test session starts ====================================================================
platform darwin -- Python 3.13.7, pytest-8.4.1, pluggy-1.6.0 -- /path/to/dir/.venv/bin/python
cachedir: .pytest_cache
rootdir: /path/to/dir
configfile: pyproject.toml
plugins: anyio-4.10.0, mock-3.14.1
collected 6 items                                                                                                                                           

sample.py::test_parametrize_ids[test1] PASSED                                                                                                  [ 16%]
sample.py::test_parametrize_ids[test2] PASSED                                                                                                  [ 33%]
sample.py::test_parametrize_ids[test3] PASSED                                                                                                  [ 50%]
sample.py::test_param_id[test1] PASSED                                                                                                         [ 66%]
sample.py::test_param_id[test2] PASSED                                                                                                         [ 83%]
sample.py::test_param_id[test3] PASSED                                                                                                         [100%]

===================================================================== 6 passed in 0.01s =====================================================================
```

## 日本語の ID を指定する

単に日本語を指定するだけでは以下のように Unicode エスケープされてしまうので何が何だか分かりません。

```log
sample.py::test_parametrize_ids[\u30c6\u30b9\u30c81] PASSED                                                                                     [ 16%]
sample.py::test_parametrize_ids[\u30c6\u30b9\u30c82] PASSED                                                                                     [ 33%]
sample.py::test_parametrize_ids[\u30c6\u30b9\u30c83] PASSED                                                                                     [ 50%]
sample.py::test_param_id[\u30c6\u30b9\u30c81] PASSED                                                                                            [ 66%]
sample.py::test_param_id[\u30c6\u30b9\u30c82] PASSED                                                                                            [ 83%]
sample.py::test_param_id[\u30c6\u30b9\u30c83] PASSED                                                                                            [100%]
```

そこで ID を日本語で表示させるために [disable_test_id_escaping_and_forfeit_all_rights_to_community_support](https://docs.pytest.org/en/stable/how-to/parametrize.html#:~:text=pytest%20by%20default%20escapes%20any%20non%2Dascii%20characters%20used%20in%20unicode%20strings%20for%20the%20parametrization%20because%20it%20has%20several%20downsides.%20If%20however%20you%20would%20like%20to%20use%20unicode%20strings%20in%20parametrization%20and%20see%20them%20in%20the%20terminal%20as%20is%20(non%2Descaped)%2C%20use%20this%20option%20in%20your%20pytest.ini%3A) というオプションを設定ファイルで有効にします。

```toml
# pyproject.toml

[tool.pytest.ini_options]
disable_test_id_escaping_and_forfeit_all_rights_to_community_support = true
```

:::message
オプションを有効にした場合に意図しない副作用やバグが発生する可能性があるとのことなので、有効にした後軽く動作の確認をすることをオススメします。

> Keep in mind however that this might cause unwanted side effects and even bugs depending on the OS used and plugins currently installed, so use it at your own risk.
>
> （ただし、使用しているOSや現在インストールされているプラグインによっては、望ましくない副作用やバグが発生する可能性があるため、自己責任で使用してください。）
:::

この状態で再度 pytest を実行すると日本語の ID が表示されるようになります。

```log
$ pytest -v sample.py
==================================================================== test session starts ====================================================================
platform darwin -- Python 3.13.7, pytest-8.4.1, pluggy-1.6.0 -- /path/to/dir/.venv/bin/python
cachedir: .pytest_cache
rootdir: /path/to/dir
configfile: pyproject.toml
plugins: anyio-4.10.0, mock-3.14.1
collected 6 items                                                                                                                                                 

sample.py::test_parametrize_ids[テスト1] PASSED                                                                                                      [ 16%]
sample.py::test_parametrize_ids[テスト2] PASSED                                                                                                      [ 33%]
sample.py::test_parametrize_ids[テスト3] PASSED                                                                                                      [ 50%]
sample.py::test_param_id[テスト1] PASSED                                                                                                             [ 66%]
sample.py::test_param_id[テスト2] PASSED                                                                                                             [ 83%]
sample.py::test_param_id[テスト3] PASSED                                                                                                             [100%]

===================================================================== 6 passed in 0.01s =====================================================================
```

## pytest のバージョンに注意

ID を設定する方法を２つ紹介致しましたが、pytest のバージョンが 8.4.0 未満で pytest.param を利用する場合、先ほど紹介したオプションを有効にしても日本語が Unicode エスケープされてしまうので注意が必要です。

```log
$ pytest -v sample.py
==================================================================== test session starts ====================================================================
platform darwin -- Python 3.13.7, pytest-8.3.5, pluggy-1.6.0 -- /path/to/dir/.venv/bin/python
cachedir: .pytest_cache
rootdir: /path/to/dir
configfile: pyproject.toml
plugins: anyio-4.10.0, mock-3.14.1
collected 6 items                                                                                                                                                 

sample.py::test_sampleparametrize_ids[テスト1] PASSED                                                                                                 [ 16%]
sample.py::test_sampleparametrize_ids[テスト2] PASSED                                                                                                 [ 33%]
sample.py::test_sampleparametrize_ids[テスト3] PASSED                                                                                                 [ 50%]
sample.py::test_sample_param_id[\u30c6\u30b9\u30c81] PASSED                                                                                          [ 66%]
sample.py::test_sample_param_id[\u30c6\u30b9\u30c82] PASSED                                                                                          [ 83%]
sample.py::test_sample_param_id[\u30c6\u30b9\u30c83] PASSED                                                                                          [100%]

===================================================================== 6 passed in 0.01s =====================================================================
```