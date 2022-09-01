{
    "nbformat_minor": 2, 
    "cells": [
        {
            "source": "<a href=\"https://www.bigdatauniversity.com\"><img src=\"https://ibm.box.com/shared/static/cw2c7r3o20w9zn8gkecaeyjhgw3xdgbj.png\" width=\"400\" align=\"center\"></a>\n\n<h1 align=\"center\"><font size=\"5\">Classification with Python</font></h1>", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "In this notebook we try to practice all the classification algorithms that we learned in this course.\n\nWe load a dataset using Pandas library, and apply the following algorithms, and find the best one for this specific dataset by accuracy evaluation methods.\n\nLets first load required libraries:", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 1, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [], 
            "source": "import itertools\nimport numpy as np\nimport matplotlib.pyplot as plt\nfrom matplotlib.ticker import NullFormatter\nimport pandas as pd\nimport numpy as np\nimport matplotlib.ticker as ticker\nfrom sklearn import preprocessing\n%matplotlib inline"
        }, 
        {
            "source": "### About dataset", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "This dataset is about past loans. The __Loan_train.csv__ data set includes details of 346 customers whose loan are already paid off or defaulted. It includes following fields:\n\n| Field          | Description                                                                           |\n|----------------|---------------------------------------------------------------------------------------|\n| Loan_status    | Whether a loan is paid off on in collection                                           |\n| Principal      | Basic principal loan amount at the                                                    |\n| Terms          | Origination terms which can be weekly (7 days), biweekly, and monthly payoff schedule |\n| Effective_date | When the loan got originated and took effects                                         |\n| Due_date       | Since it\u2019s one-time payoff schedule, each loan has one single due date                |\n| Age            | Age of applicant                                                                      |\n| Education      | Education of applicant                                                                |\n| Gender         | The gender of applicant                                                               |", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "Lets download the dataset", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 2, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "output_type": "stream", 
                    "name": "stdout", 
                    "text": "--2019-05-26 01:24:50--  https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/ML0101ENv3/labs/loan_train.csv\nResolving s3-api.us-geo.objectstorage.softlayer.net (s3-api.us-geo.objectstorage.softlayer.net)... 67.228.254.193\nConnecting to s3-api.us-geo.objectstorage.softlayer.net (s3-api.us-geo.objectstorage.softlayer.net)|67.228.254.193|:443... connected.\nHTTP request sent, awaiting response... 200 OK\nLength: 23101 (23K) [text/csv]\nSaving to: \u2018loan_train.csv\u2019\n\n100%[======================================>] 23,101      --.-K/s   in 0.002s  \n\n2019-05-26 01:24:50 (12.7 MB/s) - \u2018loan_train.csv\u2019 saved [23101/23101]\n\n"
                }
            ], 
            "source": "!wget -O loan_train.csv https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/ML0101ENv3/labs/loan_train.csv"
        }, 
        {
            "source": "### Load Data From CSV File  ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 3, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 3, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>0</td>\n      <td>0</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/8/2016</td>\n      <td>10/7/2016</td>\n      <td>45</td>\n      <td>High School or Below</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>2</td>\n      <td>2</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/8/2016</td>\n      <td>10/7/2016</td>\n      <td>33</td>\n      <td>Bechalor</td>\n      <td>female</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>3</td>\n      <td>3</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>15</td>\n      <td>9/8/2016</td>\n      <td>9/22/2016</td>\n      <td>27</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>4</td>\n      <td>4</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/9/2016</td>\n      <td>10/8/2016</td>\n      <td>28</td>\n      <td>college</td>\n      <td>female</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>6</td>\n      <td>6</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/9/2016</td>\n      <td>10/8/2016</td>\n      <td>29</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>5</th>\n      <td>7</td>\n      <td>7</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/9/2016</td>\n      <td>10/8/2016</td>\n      <td>36</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>6</th>\n      <td>8</td>\n      <td>8</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/9/2016</td>\n      <td>10/8/2016</td>\n      <td>28</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>7</th>\n      <td>9</td>\n      <td>9</td>\n      <td>PAIDOFF</td>\n      <td>800</td>\n      <td>15</td>\n      <td>9/10/2016</td>\n      <td>9/24/2016</td>\n      <td>26</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>8</th>\n      <td>10</td>\n      <td>10</td>\n      <td>PAIDOFF</td>\n      <td>300</td>\n      <td>7</td>\n      <td>9/10/2016</td>\n      <td>9/16/2016</td>\n      <td>29</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>9</th>\n      <td>11</td>\n      <td>11</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>15</td>\n      <td>9/10/2016</td>\n      <td>10/9/2016</td>\n      <td>39</td>\n      <td>High School or Below</td>\n      <td>male</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n0           0             0     PAIDOFF       1000     30       9/8/2016   \n1           2             2     PAIDOFF       1000     30       9/8/2016   \n2           3             3     PAIDOFF       1000     15       9/8/2016   \n3           4             4     PAIDOFF       1000     30       9/9/2016   \n4           6             6     PAIDOFF       1000     30       9/9/2016   \n5           7             7     PAIDOFF       1000     30       9/9/2016   \n6           8             8     PAIDOFF       1000     30       9/9/2016   \n7           9             9     PAIDOFF        800     15      9/10/2016   \n8          10            10     PAIDOFF        300      7      9/10/2016   \n9          11            11     PAIDOFF       1000     15      9/10/2016   \n\n    due_date  age             education  Gender  \n0  10/7/2016   45  High School or Below    male  \n1  10/7/2016   33              Bechalor  female  \n2  9/22/2016   27               college    male  \n3  10/8/2016   28               college  female  \n4  10/8/2016   29               college    male  \n5  10/8/2016   36               college    male  \n6  10/8/2016   28               college    male  \n7  9/24/2016   26               college    male  \n8  9/16/2016   29               college    male  \n9  10/9/2016   39  High School or Below    male  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df = pd.read_csv('loan_train.csv')\ndf.head(10)"
        }, 
        {
            "execution_count": 4, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 4, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "(346, 10)"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df.shape"
        }, 
        {
            "source": "### Convert to date time object ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 5, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 5, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>0</td>\n      <td>0</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>45</td>\n      <td>High School or Below</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>2</td>\n      <td>2</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>33</td>\n      <td>Bechalor</td>\n      <td>female</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>3</td>\n      <td>3</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>15</td>\n      <td>2016-09-08</td>\n      <td>2016-09-22</td>\n      <td>27</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>4</td>\n      <td>4</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-09</td>\n      <td>2016-10-08</td>\n      <td>28</td>\n      <td>college</td>\n      <td>female</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>6</td>\n      <td>6</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-09</td>\n      <td>2016-10-08</td>\n      <td>29</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n0           0             0     PAIDOFF       1000     30     2016-09-08   \n1           2             2     PAIDOFF       1000     30     2016-09-08   \n2           3             3     PAIDOFF       1000     15     2016-09-08   \n3           4             4     PAIDOFF       1000     30     2016-09-09   \n4           6             6     PAIDOFF       1000     30     2016-09-09   \n\n    due_date  age             education  Gender  \n0 2016-10-07   45  High School or Below    male  \n1 2016-10-07   33              Bechalor  female  \n2 2016-09-22   27               college    male  \n3 2016-10-08   28               college  female  \n4 2016-10-08   29               college    male  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df['due_date'] = pd.to_datetime(df['due_date'])\ndf['effective_date'] = pd.to_datetime(df['effective_date'])\ndf.head()"
        }, 
        {
            "source": "# Data visualization and pre-processing\n\n", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "Let\u2019s see how many of each class is in our data set ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 6, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 6, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "PAIDOFF       260\nCOLLECTION     86\nName: loan_status, dtype: int64"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df['loan_status'].value_counts()"
        }, 
        {
            "source": "260 people have paid off the loan on time while 86 have gone into collection \n", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "Lets plot some columns to underestand data better:", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 7, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "# notice: installing seaborn might takes a few minutes\n# !conda install -c anaconda seaborn -y"
        }, 
        {
            "execution_count": 8, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "output_type": "display_data", 
                    "data": {
                        "image/png": "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IIcccgjnnHMODzzwQMt2ffr0aXkkxwUXXMD8+fP53Oc+11L3nDlzGDFiBMOGDePwww9nzpw5LesuvPBCBg4cyMaNGwFYu3YtNTU1HfqdtJcft2FmZW3lO29z1HWPFK2+Z7+Vb+zsrUUEZ555JhMnTmwZtbyhoYHVq1dz4YUXMnXqVMaNG8f69es566yzuPPOO1tGiuiIL3zhCy2jnDfbsGEDp5xyCt/97nc59dRTAXjiiSeorq5uGX5p9OjRTJkypWW8vvnz57fs/+KLL3LFFVcwb948amtreeONNzjxxBM54IADGDFiBJA8W2r69OlccsklHY65I3wFZWZWZE888QRVVVVcfPHFLWV1dXUsX76cY489lnHjxgGwyy67cPvtt3PTTTcV7dj33XcfRx99dEtyAvjsZz/b8kDEtkyZMoVrr72W2tpaAGpra7nmmmu49dZbW7aZPHkyt912G5s3by5a3Pk4QZmZFdnSpUs/8UgMyP+ojCFDhvDhhx/ywQcfdPg4ud12dXV1fPTRR60eu73a8ziPwYMHc9xxx/HjH/+408dpD3fxmZl1k20fi5GrtfLtydfFV6h8MeYru/baaznttNM45ZRTinr8XL6CMjMrskMPPZRFixblLd92XMXXX3+dPn360Ldv3y49dkf23zbGfI/zOPDAA6mrq+PBBx/s9LHa4gRlZlZkY8aMYePGjdx9990tZc8//zxDhw5lwYIF/PrXvwaSBxtefvnlXHnllUU79he/+EV+85vf8POf/7yl7JFHHuGll15q1/5XXHEF3/72t2lsbASgsbGRG2+8ka9//euf2PYb3/gGU6ZMKUrc+biLz8zK2oCB+7XrzruO1NcWScyePZvJkydz00030bt3b2pqavje977HQw89xFe/+lUuvfRStmzZwvnnn7/VreUzZszY6rbuZ55JngE7YsQIdtghuaY455xzGDFiBA888MBWz5O68847OeaYY5g7dy6TJ09m8uTJVFVVMWLECL7//e+36/XV1dVx8803c+qpp7Jp0yaqqqq45ZZbWp4QnOvQQw9l1KhR7X50fUe163EbXc2PE7Du4MdtVAY/biM7Cn3cRptdfJKmS3pP0tKcslslvSppiaTZknZPy2skfSSpIZ1+2N5AzMzMcrXnM6gZwLbXx/OAwyJiBLAcuCZn3e8joi6dLsbMzKwT2kxQEfEk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\n", 
                        "text/plain": "<matplotlib.figure.Figure at 0x7f6e05871780>"
                    }, 
                    "metadata": {}
                }
            ], 
            "source": "import seaborn as sns\n\nbins = np.linspace(df.Principal.min(), df.Principal.max(), 10)\ng = sns.FacetGrid(df, col=\"Gender\", hue=\"loan_status\", palette=\"Set1\", col_wrap=2)\ng.map(plt.hist, 'Principal', bins=bins, ec=\"k\")\n\ng.axes[-1].legend()\nplt.show()"
        }, 
        {
            "execution_count": 9, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "output_type": "display_data", 
                    "data": {
                        "image/png": "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ZMCAAaxbtw6AlStXMnjw4Hb9TQrhufgKMKi6mg+XLSt2GGbWQUs//ojDb3g6tfpe/tfxbW4TEZxxxhlMmDChZdbyhoYGPv30Uy688ELuu+8+xo0bx5o1azjzzDO59957W2aKaI/vfOc7LbOcN1u7di0nnXQSd9xxB6eccgoAs2bNoqqqqmX6pdGjR3P77be3zNc3e/bslve//vrrXHXVVcycOZPa2lo++OADTjjhBPbZZx+GDx8O5O4t9eCDD3LppZe2O+ZCOUEVIK17yfguuGY9x6xZs6ioqOCSSy5pKaurq+OBBx5g1KhRjBs3DoAddtiBu+++m9GjR3coQeXzyCOPcOSRR7YkJ4Djjjuu4PfffvvtXH/99dTW1gJQW1vLddddx2233cavf/1rACZNmsSdd97J9773vVRizsddfGZmXWDhwoWb3RID8t8qY99992X16tV88cUX7d5P6267uro6vvrqqy3uu1CF3M5j4MCBHH300S0Jqyv4DMrMrBtteluM1rZUvjX5uvg6K1+M+cquv/56Tj31VE466aRU99/MZ1BmZl3goIMOYt68eXnL586du1HZ+++/T58+fejbt2+X7rs97980xny389hvv/2oq6vj8ccf7/C+tsYJysysC4wZM4Z169bxy1/+sqXs1VdfZciQIcyZM4dnn30WyN3Y8IorruDqq69Obd/f/e53+fOf/8zvf//7lrKnn36aN954o6D3X3XVVfz0pz+lsbERgMbGRm666SauvPLKzbb90Y9+xO23355K3JtyF5+Zlb3qAXsXNPKuPfW1RRLTpk1j0qRJ3HzzzVRWVjJ48GDuuusunnzySS6//HImTpzIhg0bOP/88zcaWj5lypSNhnW/9NJLAAwfPpxttsmdV5xzzjkMHz6cxx57bKP7Sd17770cddRRzJgxg0mTJjFp0iQqKioYPnw4P/vZzwo6vrq6Om655RZOOeUUvv76ayoqKrj11ltb7hDc2kEHHcTIkSMLvnV9exR0u4201NfXx6anjaUgrdtk1HzS5NttlL8O3W6jtVJtJ1ni221kR3fcbsPMzKxbOUGZmVkmOUGZWVlyF3jxdfZv4ARlZmWnsrKSVatWOUkVUUSwatUqKisrO1yHR/GZWdmpqamhqamJFStWFDuUHq2yspKamo4PDHOCKlHb0bFfneczsH9/lixdmkpdZllQ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\n", 
                        "text/plain": "<matplotlib.figure.Figure at 0x7f6dfd0f5a90>"
                    }, 
                    "metadata": {}
                }
            ], 
            "source": "bins = np.linspace(df.age.min(), df.age.max(), 10)\ng = sns.FacetGrid(df, col=\"Gender\", hue=\"loan_status\", palette=\"Set1\", col_wrap=2)\ng.map(plt.hist, 'age', bins=bins, ec=\"k\")\n\ng.axes[-1].legend()\nplt.show()"
        }, 
        {
            "source": "# Pre-processing:  Feature selection/extraction", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "### Lets look at the day of the week people get the loan ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 10, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "output_type": "display_data", 
                    "data": {
                        "image/png": "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\n", 
                        "text/plain": "<matplotlib.figure.Figure at 0x7f6dfcff8a20>"
                    }, 
                    "metadata": {}
                }
            ], 
            "source": "df['dayofweek'] = df['effective_date'].dt.dayofweek\nbins = np.linspace(df.dayofweek.min(), df.dayofweek.max(), 10)\ng = sns.FacetGrid(df, col=\"Gender\", hue=\"loan_status\", palette=\"Set1\", col_wrap=2)\ng.map(plt.hist, 'dayofweek', bins=bins, ec=\"k\")\ng.axes[-1].legend()\nplt.show()\n"
        }, 
        {
            "source": "We see that people who get the loan at the end of the week dont pay it off, so lets use Feature binarization to set a threshold values less then day 4 ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 11, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 11, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n      <th>dayofweek</th>\n      <th>weekend</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>0</td>\n      <td>0</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>45</td>\n      <td>High School or Below</td>\n      <td>male</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>2</td>\n      <td>2</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>33</td>\n      <td>Bechalor</td>\n      <td>female</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>3</td>\n      <td>3</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>15</td>\n      <td>2016-09-08</td>\n      <td>2016-09-22</td>\n      <td>27</td>\n      <td>college</td>\n      <td>male</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>4</td>\n      <td>4</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-09</td>\n      <td>2016-10-08</td>\n      <td>28</td>\n      <td>college</td>\n      <td>female</td>\n      <td>4</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>6</td>\n      <td>6</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-09</td>\n      <td>2016-10-08</td>\n      <td>29</td>\n      <td>college</td>\n      <td>male</td>\n      <td>4</td>\n      <td>1</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n0           0             0     PAIDOFF       1000     30     2016-09-08   \n1           2             2     PAIDOFF       1000     30     2016-09-08   \n2           3             3     PAIDOFF       1000     15     2016-09-08   \n3           4             4     PAIDOFF       1000     30     2016-09-09   \n4           6             6     PAIDOFF       1000     30     2016-09-09   \n\n    due_date  age             education  Gender  dayofweek  weekend  \n0 2016-10-07   45  High School or Below    male          3        0  \n1 2016-10-07   33              Bechalor  female          3        0  \n2 2016-09-22   27               college    male          3        0  \n3 2016-10-08   28               college  female          4        1  \n4 2016-10-08   29               college    male          4        1  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df['weekend'] = df['dayofweek'].apply(lambda x: 1 if (x>3)  else 0)\ndf.head()"
        }, 
        {
            "source": "## Convert Categorical features to numerical values", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "Lets look at gender:", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 12, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 12, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "Gender  loan_status\nfemale  PAIDOFF        0.865385\n        COLLECTION     0.134615\nmale    PAIDOFF        0.731293\n        COLLECTION     0.268707\nName: loan_status, dtype: float64"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df.groupby(['Gender'])['loan_status'].value_counts(normalize=True)"
        }, 
        {
            "source": "86 % of female pay there loans while only 73 % of males pay there loan\n", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "Lets convert male to 0 and female to 1:\n", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 13, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 13, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n      <th>dayofweek</th>\n      <th>weekend</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>0</td>\n      <td>0</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>45</td>\n      <td>High School or Below</td>\n      <td>0</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>2</td>\n      <td>2</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>33</td>\n      <td>Bechalor</td>\n      <td>1</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>3</td>\n      <td>3</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>15</td>\n      <td>2016-09-08</td>\n      <td>2016-09-22</td>\n      <td>27</td>\n      <td>college</td>\n      <td>0</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>4</td>\n      <td>4</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-09</td>\n      <td>2016-10-08</td>\n      <td>28</td>\n      <td>college</td>\n      <td>1</td>\n      <td>4</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>6</td>\n      <td>6</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-09</td>\n      <td>2016-10-08</td>\n      <td>29</td>\n      <td>college</td>\n      <td>0</td>\n      <td>4</td>\n      <td>1</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n0           0             0     PAIDOFF       1000     30     2016-09-08   \n1           2             2     PAIDOFF       1000     30     2016-09-08   \n2           3             3     PAIDOFF       1000     15     2016-09-08   \n3           4             4     PAIDOFF       1000     30     2016-09-09   \n4           6             6     PAIDOFF       1000     30     2016-09-09   \n\n    due_date  age             education  Gender  dayofweek  weekend  \n0 2016-10-07   45  High School or Below       0          3        0  \n1 2016-10-07   33              Bechalor       1          3        0  \n2 2016-09-22   27               college       0          3        0  \n3 2016-10-08   28               college       1          4        1  \n4 2016-10-08   29               college       0          4        1  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df['Gender'].replace(to_replace=['male','female'], value=[0,1],inplace=True)\ndf.head()"
        }, 
        {
            "source": "## One Hot Encoding  \n#### How about education?", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 14, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 14, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "education             loan_status\nBechalor              PAIDOFF        0.750000\n                      COLLECTION     0.250000\nHigh School or Below  PAIDOFF        0.741722\n                      COLLECTION     0.258278\nMaster or Above       COLLECTION     0.500000\n                      PAIDOFF        0.500000\ncollege               PAIDOFF        0.765101\n                      COLLECTION     0.234899\nName: loan_status, dtype: float64"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df.groupby(['education'])['loan_status'].value_counts(normalize=True)"
        }, 
        {
            "source": "#### Feature befor One Hot Encoding", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 15, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 15, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>age</th>\n      <th>Gender</th>\n      <th>education</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>45</td>\n      <td>0</td>\n      <td>High School or Below</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>33</td>\n      <td>1</td>\n      <td>Bechalor</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>1000</td>\n      <td>15</td>\n      <td>27</td>\n      <td>0</td>\n      <td>college</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>28</td>\n      <td>1</td>\n      <td>college</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>29</td>\n      <td>0</td>\n      <td>college</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Principal  terms  age  Gender             education\n0       1000     30   45       0  High School or Below\n1       1000     30   33       1              Bechalor\n2       1000     15   27       0               college\n3       1000     30   28       1               college\n4       1000     30   29       0               college"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df[['Principal','terms','age','Gender','education']].head()"
        }, 
        {
            "source": "#### Use one hot encoding technique to conver categorical varables to binary variables and append them to the feature Data Frame ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 16, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 16, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>age</th>\n      <th>Gender</th>\n      <th>weekend</th>\n      <th>Bechalor</th>\n      <th>High School or Below</th>\n      <th>college</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>45</td>\n      <td>0</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>33</td>\n      <td>1</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>1000</td>\n      <td>15</td>\n      <td>27</td>\n      <td>0</td>\n      <td>0</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>28</td>\n      <td>1</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>29</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Principal  terms  age  Gender  weekend  Bechalor  High School or Below  \\\n0       1000     30   45       0        0         0                     1   \n1       1000     30   33       1        0         1                     0   \n2       1000     15   27       0        0         0                     0   \n3       1000     30   28       1        1         0                     0   \n4       1000     30   29       0        1         0                     0   \n\n   college  \n0        0  \n1        0  \n2        1  \n3        1  \n4        1  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "Feature = df[['Principal','terms','age','Gender','weekend']]\nFeature = pd.concat([Feature,pd.get_dummies(df['education'])], axis=1)\nFeature.drop(['Master or Above'], axis = 1,inplace=True)\nFeature.head()\n"
        }, 
        {
            "source": "### Feature selection", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "Lets defind feature sets, X:", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 17, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 17, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>age</th>\n      <th>Gender</th>\n      <th>weekend</th>\n      <th>Bechalor</th>\n      <th>High School or Below</th>\n      <th>college</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>45</td>\n      <td>0</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>33</td>\n      <td>1</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>1000</td>\n      <td>15</td>\n      <td>27</td>\n      <td>0</td>\n      <td>0</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>28</td>\n      <td>1</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>29</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Principal  terms  age  Gender  weekend  Bechalor  High School or Below  \\\n0       1000     30   45       0        0         0                     1   \n1       1000     30   33       1        0         1                     0   \n2       1000     15   27       0        0         0                     0   \n3       1000     30   28       1        1         0                     0   \n4       1000     30   29       0        1         0                     0   \n\n   college  \n0        0  \n1        0  \n2        1  \n3        1  \n4        1  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "X = Feature\nX[0:5]"
        }, 
        {
            "source": "What are our lables?", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "### Replacing \"PAIDOFF\" and \"COLLECTION\" strings with 0 and 1 integers because some models can only work with integers or floats", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 18, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 18, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n      <th>dayofweek</th>\n      <th>weekend</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>0</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>45</td>\n      <td>High School or Below</td>\n      <td>0</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>2</td>\n      <td>2</td>\n      <td>0</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>33</td>\n      <td>Bechalor</td>\n      <td>1</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>3</td>\n      <td>3</td>\n      <td>0</td>\n      <td>1000</td>\n      <td>15</td>\n      <td>2016-09-08</td>\n      <td>2016-09-22</td>\n      <td>27</td>\n      <td>college</td>\n      <td>0</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>4</td>\n      <td>4</td>\n      <td>0</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-09</td>\n      <td>2016-10-08</td>\n      <td>28</td>\n      <td>college</td>\n      <td>1</td>\n      <td>4</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>6</td>\n      <td>6</td>\n      <td>0</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-09</td>\n      <td>2016-10-08</td>\n      <td>29</td>\n      <td>college</td>\n      <td>0</td>\n      <td>4</td>\n      <td>1</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1  loan_status  Principal  terms effective_date  \\\n0           0             0            0       1000     30     2016-09-08   \n1           2             2            0       1000     30     2016-09-08   \n2           3             3            0       1000     15     2016-09-08   \n3           4             4            0       1000     30     2016-09-09   \n4           6             6            0       1000     30     2016-09-09   \n\n    due_date  age             education  Gender  dayofweek  weekend  \n0 2016-10-07   45  High School or Below       0          3        0  \n1 2016-10-07   33              Bechalor       1          3        0  \n2 2016-09-22   27               college       0          3        0  \n3 2016-10-08   28               college       1          4        1  \n4 2016-10-08   29               college       0          4        1  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "df['loan_status'].replace(to_replace=['PAIDOFF','COLLECTION'], value=[0,1],inplace=True)\ndf.head()"
        }, 
        {
            "execution_count": 19, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 19, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0])"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "y = df['loan_status'].values\ny[0:20]"
        }, 
        {
            "source": "## Doing Train test split -", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 20, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "from sklearn.model_selection import train_test_split\nX_train, X_test, y_train, y_test = train_test_split( X, y, test_size=0.40, random_state=5)"
        }, 
        {
            "execution_count": 21, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "output_type": "stream", 
                    "name": "stdout", 
                    "text": "Train set: (207, 8) (207,)\nTest set: (139, 8) (139,)\n"
                }
            ], 
            "source": "print ('Train set:', X_train.shape,  y_train.shape)\nprint ('Test set:', X_test.shape,  y_test.shape)"
        }, 
        {
            "execution_count": 22, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "output_type": "display_data", 
                    "data": {
                        "text/plain": "(207, 8)"
                    }, 
                    "metadata": {}
                }, 
                {
                    "output_type": "display_data", 
                    "data": {
                        "text/plain": "(207,)"
                    }, 
                    "metadata": {}
                }, 
                {
                    "output_type": "display_data", 
                    "data": {
                        "text/plain": "(139, 8)"
                    }, 
                    "metadata": {}
                }, 
                {
                    "output_type": "display_data", 
                    "data": {
                        "text/plain": "(139,)"
                    }, 
                    "metadata": {}
                }
            ], 
            "source": "display(X_train.shape, y_train.shape)\ndisplay(X_test.shape, y_test.shape)"
        }, 
        {
            "source": "## Normalize Data ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "Data Standardization give data zero mean and unit variance (technically should be done after train test split )", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 23, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 23, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "array([[ 0.52533066, -0.97207061,  0.53495582, -0.45109685,  0.85993942,\n         2.76134025, -0.86846836, -0.91206272],\n       [ 0.52533066,  0.91317564, -0.47326137,  2.21681883,  0.85993942,\n        -0.36214298, -0.86846836,  1.09641582],\n       [ 0.52533066,  0.91317564, -0.13718897, -0.45109685,  0.85993942,\n        -0.36214298,  1.15145243, -0.91206272],\n       [ 0.52533066,  0.91317564, -0.64129757, -0.45109685,  0.85993942,\n        -0.36214298, -0.86846836,  1.09641582],\n       [ 0.52533066, -0.97207061, -0.47326137,  2.21681883, -1.16287262,\n        -0.36214298, -0.86846836,  1.09641582]])"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "# (technically should be done after train test split )\nX_train = preprocessing.StandardScaler().fit(X_train).transform(X_train)\nX_test = preprocessing.StandardScaler().fit(X_test).transform(X_test)\nX_train[0:5]"
        }, 
        {
            "source": "# Classification ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "Now, it is your turn, use the training set to build an accurate model. Then use the test set to report the accuracy of the model\nYou should use the following algorithm:\n- K Nearest Neighbor(KNN)\n- Decision Tree\n- Support Vector Machine\n- Logistic Regression\n\n\n\n__ Notice:__ \n- You can go above and change the pre-processing, feature selection, feature-extraction, and so on, to make a better model.\n- You should use either scikit-learn, Scipy or Numpy libraries for developing the classification algorithms.\n- You should include the code of the algorithm in the following cells.", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "source": "# K Nearest Neighbor(KNN)\nNotice: You should find the best k to build the model with the best accuracy.  \n**warning:** You should not use the __loan_test.csv__ for finding the best k, however, you can split your train_loan.csv into train and test to find the best __k__.", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 24, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "from sklearn.neighbors import KNeighborsClassifier\nfrom sklearn.metrics import accuracy_score"
        }, 
        {
            "execution_count": 25, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 25, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "array([ 0.65467626,  0.72661871,  0.67625899,  0.72661871,  0.69064748,\n        0.69064748,  0.64748201,  0.69064748,  0.63309353,  0.69784173,\n        0.69064748,  0.69784173,  0.69064748,  0.69784173,  0.6618705 ,\n        0.69064748,  0.6618705 ,  0.67625899,  0.67625899,  0.67625899,\n        0.70503597,  0.71223022,  0.72661871,  0.74100719,  0.74100719])"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "# We will be checking for value of K from 0 to 25 so\nn = 25\naccuracy = np.zeros(n)\nfor i in range(1,n+1):\n    clf = KNeighborsClassifier(n_neighbors = i).fit(X_train, y_train)\n    y_test_predicted = clf.predict(X_test)\n    accuracy[i-1] = (accuracy_score(y_test, y_test_predicted))\naccuracy"
        }, 
        {
            "execution_count": 26, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "output_type": "display_data", 
                    "data": {
                        "image/png": "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\n", 
                        "text/plain": "<matplotlib.figure.Figure at 0x7f6dfd0863c8>"
                    }, 
                    "metadata": {}
                }, 
                {
                    "output_type": "stream", 
                    "name": "stdout", 
                    "text": "Maximum Accuracy Got is - \n"
                }, 
                {
                    "execution_count": 26, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>0</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>24</th>\n      <td>0.741007</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "           0\n24  0.741007"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "plt.plot(range(1,n+1),accuracy,'g')\nplt.ylabel('Accuracy')\nplt.xlabel('Number of Neighbors (K)')\nplt.show()\n\naccuracy = pd.DataFrame(accuracy)\nprint(\"Maximum Accuracy Got is - \" )\naccuracy.sort_values(by = 0, ascending = False)[0:1]"
        }, 
        {
            "execution_count": 27, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "clf_KNN = KNeighborsClassifier(n_neighbors = 24).fit(X_train, y_train)\n# y_test_pred_KNN = clf_KNN.predict(X_test)"
        }, 
        {
            "source": "# Decision Tree", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 28, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "from sklearn.tree import DecisionTreeClassifier"
        }, 
        {
            "execution_count": 29, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "output_type": "stream", 
                    "name": "stdout", 
                    "text": "Accuracy using criterion as gini -  0.741007194245\nAccuracy using criterion as entropy -  0.654676258993\n"
                }
            ], 
            "source": "clf2 = DecisionTreeClassifier(criterion = 'gini').fit(X_train, y_train)\ny_test_pred_KNN = clf.predict(X_test)\nprint(\"Accuracy using criterion as gini - \", accuracy_score(y_test, y_test_pred_KNN))\nclf3 = DecisionTreeClassifier(criterion = 'entropy').fit(X_train, y_train)\ny_test_pred_KNN = clf2.predict(X_test)\nprint(\"Accuracy using criterion as entropy - \", accuracy_score(y_test, y_test_pred_KNN))"
        }, 
        {
            "execution_count": 30, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "# using criterion as gini\nclf_DT = DecisionTreeClassifier(criterion = 'gini').fit(X_train, y_train)"
        }, 
        {
            "source": "# Support Vector Machine", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 31, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "from sklearn.svm import SVC"
        }, 
        {
            "execution_count": 32, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "output_type": "stream", 
                    "name": "stdout", 
                    "text": "accuracy using polynomial kernel -  0.654676258993\naccuracy using Radial Basis function kernel -  0.640287769784\n"
                }
            ], 
            "source": "clf4 = SVC(kernel = 'poly').fit(X_train, y_train)\nprint(\"accuracy using polynomial kernel - \", accuracy_score(y_test, clf2.predict(X_test)))\nclf5 = SVC(kernel = 'rbf').fit(X_train, y_train)\nprint(\"accuracy using Radial Basis function kernel - \", accuracy_score(y_test, clf3.predict(X_test)))"
        }, 
        {
            "execution_count": 33, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "# using linear for kernel in our SVM model\nclf_SVM = SVC(kernel = 'poly', random_state = 4).fit(X_train, y_train)"
        }, 
        {
            "source": "# Logistic Regression", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 34, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "from sklearn.linear_model import LogisticRegression"
        }, 
        {
            "execution_count": 35, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 35, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "LogisticRegression(C=1.0, class_weight=None, dual=False, fit_intercept=True,\n          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,\n          penalty='l2', random_state=None, solver='lbfgs', tol=0.0001,\n          verbose=0, warm_start=True)"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "clf_LR = LogisticRegression(solver='lbfgs', warm_start = True)\nclf_LR.fit(X_train, y_train)\n#print(\"accuracy score - \", accuracy_score(y_test, clf_LR.predict(X_test)))"
        }, 
        {
            "source": "# Model Evaluation using Test set", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 36, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "from sklearn.metrics import jaccard_similarity_score\nfrom sklearn.metrics import f1_score\nfrom sklearn.metrics import log_loss"
        }, 
        {
            "source": "First, download and load the test set:", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 37, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "output_type": "stream", 
                    "name": "stdout", 
                    "text": "--2019-05-26 01:25:52--  https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/ML0101ENv3/labs/loan_test.csv\nResolving s3-api.us-geo.objectstorage.softlayer.net (s3-api.us-geo.objectstorage.softlayer.net)... 67.228.254.193\nConnecting to s3-api.us-geo.objectstorage.softlayer.net (s3-api.us-geo.objectstorage.softlayer.net)|67.228.254.193|:443... connected.\nHTTP request sent, awaiting response... 200 OK\nLength: 3642 (3.6K) [text/csv]\nSaving to: \u2018loan_test.csv\u2019\n\n100%[======================================>] 3,642       --.-K/s   in 0s      \n\n2019-05-26 01:25:52 (585 MB/s) - \u2018loan_test.csv\u2019 saved [3642/3642]\n\n"
                }
            ], 
            "source": "!wget -O loan_test.csv https://s3-api.us-geo.objectstorage.softlayer.net/cf-courses-data/CognitiveClass/ML0101ENv3/labs/loan_test.csv"
        }, 
        {
            "source": "### Load Test set for evaluation ", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": 38, 
            "cell_type": "code", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }, 
            "outputs": [
                {
                    "execution_count": 38, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1</td>\n      <td>1</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/8/2016</td>\n      <td>10/7/2016</td>\n      <td>50</td>\n      <td>Bechalor</td>\n      <td>female</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>5</td>\n      <td>5</td>\n      <td>PAIDOFF</td>\n      <td>300</td>\n      <td>7</td>\n      <td>9/9/2016</td>\n      <td>9/15/2016</td>\n      <td>35</td>\n      <td>Master or Above</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>21</td>\n      <td>21</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/10/2016</td>\n      <td>10/9/2016</td>\n      <td>43</td>\n      <td>High School or Below</td>\n      <td>female</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>24</td>\n      <td>24</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>9/10/2016</td>\n      <td>10/9/2016</td>\n      <td>26</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>35</td>\n      <td>35</td>\n      <td>PAIDOFF</td>\n      <td>800</td>\n      <td>15</td>\n      <td>9/11/2016</td>\n      <td>9/25/2016</td>\n      <td>29</td>\n      <td>Bechalor</td>\n      <td>male</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n0           1             1     PAIDOFF       1000     30       9/8/2016   \n1           5             5     PAIDOFF        300      7       9/9/2016   \n2          21            21     PAIDOFF       1000     30      9/10/2016   \n3          24            24     PAIDOFF       1000     30      9/10/2016   \n4          35            35     PAIDOFF        800     15      9/11/2016   \n\n    due_date  age             education  Gender  \n0  10/7/2016   50              Bechalor  female  \n1  9/15/2016   35       Master or Above    male  \n2  10/9/2016   43  High School or Below  female  \n3  10/9/2016   26               college    male  \n4  9/25/2016   29              Bechalor    male  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "test_df = pd.read_csv('loan_test.csv')\ntest_df.head()"
        }, 
        {
            "source": "# Test Data Pre-processing:  Feature selection/extraction same as done with train data", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 39, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 39, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1</td>\n      <td>1</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>50</td>\n      <td>Bechalor</td>\n      <td>female</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>5</td>\n      <td>5</td>\n      <td>PAIDOFF</td>\n      <td>300</td>\n      <td>7</td>\n      <td>2016-09-09</td>\n      <td>2016-09-15</td>\n      <td>35</td>\n      <td>Master or Above</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>21</td>\n      <td>21</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-10</td>\n      <td>2016-10-09</td>\n      <td>43</td>\n      <td>High School or Below</td>\n      <td>female</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>24</td>\n      <td>24</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-10</td>\n      <td>2016-10-09</td>\n      <td>26</td>\n      <td>college</td>\n      <td>male</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>35</td>\n      <td>35</td>\n      <td>PAIDOFF</td>\n      <td>800</td>\n      <td>15</td>\n      <td>2016-09-11</td>\n      <td>2016-09-25</td>\n      <td>29</td>\n      <td>Bechalor</td>\n      <td>male</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n0           1             1     PAIDOFF       1000     30     2016-09-08   \n1           5             5     PAIDOFF        300      7     2016-09-09   \n2          21            21     PAIDOFF       1000     30     2016-09-10   \n3          24            24     PAIDOFF       1000     30     2016-09-10   \n4          35            35     PAIDOFF        800     15     2016-09-11   \n\n    due_date  age             education  Gender  \n0 2016-10-07   50              Bechalor  female  \n1 2016-09-15   35       Master or Above    male  \n2 2016-10-09   43  High School or Below  female  \n3 2016-10-09   26               college    male  \n4 2016-09-25   29              Bechalor    male  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "# conversion to datetime object\ntest_df['due_date'] = pd.to_datetime(test_df['due_date'])\ntest_df['effective_date'] = pd.to_datetime(test_df['effective_date'])\ntest_df.head()"
        }, 
        {
            "execution_count": 40, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 40, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n      <th>dayofweek</th>\n      <th>weekend</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1</td>\n      <td>1</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>50</td>\n      <td>Bechalor</td>\n      <td>female</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>5</td>\n      <td>5</td>\n      <td>PAIDOFF</td>\n      <td>300</td>\n      <td>7</td>\n      <td>2016-09-09</td>\n      <td>2016-09-15</td>\n      <td>35</td>\n      <td>Master or Above</td>\n      <td>male</td>\n      <td>4</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>21</td>\n      <td>21</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-10</td>\n      <td>2016-10-09</td>\n      <td>43</td>\n      <td>High School or Below</td>\n      <td>female</td>\n      <td>5</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>24</td>\n      <td>24</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-10</td>\n      <td>2016-10-09</td>\n      <td>26</td>\n      <td>college</td>\n      <td>male</td>\n      <td>5</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>35</td>\n      <td>35</td>\n      <td>PAIDOFF</td>\n      <td>800</td>\n      <td>15</td>\n      <td>2016-09-11</td>\n      <td>2016-09-25</td>\n      <td>29</td>\n      <td>Bechalor</td>\n      <td>male</td>\n      <td>6</td>\n      <td>1</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n0           1             1     PAIDOFF       1000     30     2016-09-08   \n1           5             5     PAIDOFF        300      7     2016-09-09   \n2          21            21     PAIDOFF       1000     30     2016-09-10   \n3          24            24     PAIDOFF       1000     30     2016-09-10   \n4          35            35     PAIDOFF        800     15     2016-09-11   \n\n    due_date  age             education  Gender  dayofweek  weekend  \n0 2016-10-07   50              Bechalor  female          3        0  \n1 2016-09-15   35       Master or Above    male          4        1  \n2 2016-10-09   43  High School or Below  female          5        1  \n3 2016-10-09   26               college    male          5        1  \n4 2016-09-25   29              Bechalor    male          6        1  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "# creating weekend column\ntest_df['dayofweek'] = test_df['effective_date'].dt.dayofweek\ntest_df['weekend'] = test_df['dayofweek'].apply(lambda x: 1 if (x>3)  else 0)\ntest_df.head()"
        }, 
        {
            "execution_count": 41, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 41, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Unnamed: 0</th>\n      <th>Unnamed: 0.1</th>\n      <th>loan_status</th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>effective_date</th>\n      <th>due_date</th>\n      <th>age</th>\n      <th>education</th>\n      <th>Gender</th>\n      <th>dayofweek</th>\n      <th>weekend</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1</td>\n      <td>1</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-08</td>\n      <td>2016-10-07</td>\n      <td>50</td>\n      <td>Bechalor</td>\n      <td>1</td>\n      <td>3</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>5</td>\n      <td>5</td>\n      <td>PAIDOFF</td>\n      <td>300</td>\n      <td>7</td>\n      <td>2016-09-09</td>\n      <td>2016-09-15</td>\n      <td>35</td>\n      <td>Master or Above</td>\n      <td>0</td>\n      <td>4</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>21</td>\n      <td>21</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-10</td>\n      <td>2016-10-09</td>\n      <td>43</td>\n      <td>High School or Below</td>\n      <td>1</td>\n      <td>5</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>24</td>\n      <td>24</td>\n      <td>PAIDOFF</td>\n      <td>1000</td>\n      <td>30</td>\n      <td>2016-09-10</td>\n      <td>2016-10-09</td>\n      <td>26</td>\n      <td>college</td>\n      <td>0</td>\n      <td>5</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>35</td>\n      <td>35</td>\n      <td>PAIDOFF</td>\n      <td>800</td>\n      <td>15</td>\n      <td>2016-09-11</td>\n      <td>2016-09-25</td>\n      <td>29</td>\n      <td>Bechalor</td>\n      <td>0</td>\n      <td>6</td>\n      <td>1</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Unnamed: 0  Unnamed: 0.1 loan_status  Principal  terms effective_date  \\\n0           1             1     PAIDOFF       1000     30     2016-09-08   \n1           5             5     PAIDOFF        300      7     2016-09-09   \n2          21            21     PAIDOFF       1000     30     2016-09-10   \n3          24            24     PAIDOFF       1000     30     2016-09-10   \n4          35            35     PAIDOFF        800     15     2016-09-11   \n\n    due_date  age             education  Gender  dayofweek  weekend  \n0 2016-10-07   50              Bechalor       1          3        0  \n1 2016-09-15   35       Master or Above       0          4        1  \n2 2016-10-09   43  High School or Below       1          5        1  \n3 2016-10-09   26               college       0          5        1  \n4 2016-09-25   29              Bechalor       0          6        1  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "# replacing male and female string with 0 and 1\ntest_df['Gender'].replace(to_replace=['male','female'], value=[0,1],inplace=True)\ntest_df.head()"
        }, 
        {
            "execution_count": 42, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 42, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>Principal</th>\n      <th>terms</th>\n      <th>age</th>\n      <th>Gender</th>\n      <th>weekend</th>\n      <th>Bechalor</th>\n      <th>High School or Below</th>\n      <th>college</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>50</td>\n      <td>1</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>300</td>\n      <td>7</td>\n      <td>35</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>43</td>\n      <td>1</td>\n      <td>1</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>1000</td>\n      <td>30</td>\n      <td>26</td>\n      <td>0</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n      <td>1</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>800</td>\n      <td>15</td>\n      <td>29</td>\n      <td>0</td>\n      <td>1</td>\n      <td>1</td>\n      <td>0</td>\n      <td>0</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "   Principal  terms  age  Gender  weekend  Bechalor  High School or Below  \\\n0       1000     30   50       1        0         1                     0   \n1        300      7   35       0        1         0                     0   \n2       1000     30   43       1        1         0                     1   \n3       1000     30   26       0        1         0                     0   \n4        800     15   29       0        1         1                     0   \n\n   college  \n0        0  \n1        0  \n2        0  \n3        1  \n4        0  "
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "# creaing dummies and dropping one column\n\nX_t = test_df[['Principal','terms','age','Gender','weekend']]\nX_t = pd.concat([X_t,pd.get_dummies(test_df['education'])], axis=1)\nX_t.drop(['Master or Above'], axis = 1,inplace=True)\nX_t.head()"
        }, 
        {
            "execution_count": 43, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 43, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "array([[ 0.49362588,  0.92844966,  3.05981865,  1.97714211, -1.30384048,\n         2.39791576, -0.79772404, -0.86135677],\n       [-3.56269116, -1.70427745,  0.53336288, -0.50578054,  0.76696499,\n        -0.41702883, -0.79772404, -0.86135677],\n       [ 0.49362588,  0.92844966,  1.88080596,  1.97714211,  0.76696499,\n        -0.41702883,  1.25356634, -0.86135677],\n       [ 0.49362588,  0.92844966, -0.98251057, -0.50578054,  0.76696499,\n        -0.41702883, -0.79772404,  1.16095912],\n       [-0.66532184, -0.78854628, -0.47721942, -0.50578054,  0.76696499,\n         2.39791576, -0.79772404, -0.86135677]])"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "# Feature Scaling\nX_t = preprocessing.StandardScaler().fit(X_t).transform(X_t)\nX_t[0:5]"
        }, 
        {
            "execution_count": 44, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "test_df['loan_status'].replace(to_replace=['PAIDOFF','COLLECTION'], value=[0,1],inplace=True)"
        }, 
        {
            "execution_count": 45, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 45, 
                    "metadata": {}, 
                    "data": {
                        "text/plain": "array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0])"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "y_t = test_df['loan_status'].values\ny_t[0:20]"
        }, 
        {
            "execution_count": 46, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "# np arrays to store intermediate result\nJaccard = np.full(4, np.nan)\nF1_score = np.full(4, np.nan)\nLogLoss = np.full(4, np.nan)\nAlgorithm = np.array(4)\nAlgorithm = [\"KNN\", \"Decision Tree\", \"SVM\", \"LogisticRegression\"]"
        }, 
        {
            "execution_count": 47, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "Jaccard[0] = jaccard_similarity_score(y_t, clf_KNN.predict(X_t))\nJaccard[1] = jaccard_similarity_score(y_t, clf_DT.predict(X_t))\nJaccard[2] = jaccard_similarity_score(y_t, clf_SVM.predict(X_t))\nJaccard[3] = jaccard_similarity_score(y_t, clf_LR.predict(X_t))"
        }, 
        {
            "execution_count": 48, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "F1_score[0] = f1_score(y_t, clf_KNN.predict(X_t))\nF1_score[1] = f1_score(y_t, clf_DT.predict(X_t))\nF1_score[2] = f1_score(y_t, clf_SVM.predict(X_t))\nF1_score[3] = f1_score(y_t, clf_LR.predict(X_t))"
        }, 
        {
            "execution_count": 49, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "LogLoss[3] = log_loss(y_t, clf_LR.predict_proba(X_t))"
        }, 
        {
            "execution_count": 50, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": "Report = pd.DataFrame({\"Jaccard\":Jaccard, \"F1-score\":F1_score, \"LogLoss\":LogLoss}, index=Algorithm)"
        }, 
        {
            "source": "#  Report", 
            "cell_type": "markdown", 
            "metadata": {}
        }, 
        {
            "execution_count": 51, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [
                {
                    "execution_count": 51, 
                    "metadata": {}, 
                    "data": {
                        "text/html": "<div>\n<style scoped>\n    .dataframe tbody tr th:only-of-type {\n        vertical-align: middle;\n    }\n\n    .dataframe tbody tr th {\n        vertical-align: top;\n    }\n\n    .dataframe thead th {\n        text-align: right;\n    }\n</style>\n<table border=\"1\" class=\"dataframe\">\n  <thead>\n    <tr style=\"text-align: right;\">\n      <th></th>\n      <th>F1-score</th>\n      <th>Jaccard</th>\n      <th>LogLoss</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>KNN</th>\n      <td>0.235294</td>\n      <td>0.759259</td>\n      <td>NaN</td>\n    </tr>\n    <tr>\n      <th>Decision Tree</th>\n      <td>0.300000</td>\n      <td>0.740741</td>\n      <td>NaN</td>\n    </tr>\n    <tr>\n      <th>SVM</th>\n      <td>0.352941</td>\n      <td>0.796296</td>\n      <td>NaN</td>\n    </tr>\n    <tr>\n      <th>LogisticRegression</th>\n      <td>0.250000</td>\n      <td>0.777778</td>\n      <td>0.470967</td>\n    </tr>\n  </tbody>\n</table>\n</div>", 
                        "text/plain": "                    F1-score   Jaccard   LogLoss\nKNN                 0.235294  0.759259       NaN\nDecision Tree       0.300000  0.740741       NaN\nSVM                 0.352941  0.796296       NaN\nLogisticRegression  0.250000  0.777778  0.470967"
                    }, 
                    "output_type": "execute_result"
                }
            ], 
            "source": "Report"
        }, 
        {
            "execution_count": null, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": ""
        }, 
        {
            "source": "<h2>Want to learn more?</h2>\n\nIBM SPSS Modeler is a comprehensive analytics platform that has many machine learning algorithms. It has been designed to bring predictive intelligence to decisions made by individuals, by groups, by systems \u2013 by your enterprise as a whole. A free trial is available through this course, available here: <a href=\"http://cocl.us/ML0101EN-SPSSModeler\">SPSS Modeler</a>\n\nAlso, you can use Watson Studio to run these notebooks faster with bigger datasets. Watson Studio is IBM's leading cloud solution for data scientists, built by data scientists. With Jupyter notebooks, RStudio, Apache Spark and popular libraries pre-packaged in the cloud, Watson Studio enables data scientists to collaborate on their projects without having to install anything. Join the fast-growing community of Watson Studio users today with a free account at <a href=\"https://cocl.us/ML0101EN_DSX\">Watson Studio</a>\n\n<h3>Thanks for completing this lesson!</h3>\n\n<h4>Author:  <a href=\"https://ca.linkedin.com/in/saeedaghabozorgi\">Saeed Aghabozorgi</a></h4>\n<p><a href=\"https://ca.linkedin.com/in/saeedaghabozorgi\">Saeed Aghabozorgi</a>, PhD is a Data Scientist in IBM with a track record of developing enterprise level applications that substantially increases clients\u2019 ability to turn data into actionable knowledge. He is a researcher in data mining field and expert in developing advanced analytic methods like machine learning and statistical modelling on large datasets.</p>\n\n<hr>\n\n<p>Copyright &copy; 2018 <a href=\"https://cocl.us/DX0108EN_CC\">Cognitive Class</a>. This notebook and its source code are released under the terms of the <a href=\"https://bigdatauniversity.com/mit-license/\">MIT License</a>.</p>", 
            "cell_type": "markdown", 
            "metadata": {
                "button": false, 
                "new_sheet": false, 
                "run_control": {
                    "read_only": false
                }
            }
        }, 
        {
            "execution_count": null, 
            "cell_type": "code", 
            "metadata": {}, 
            "outputs": [], 
            "source": ""
        }
    ], 
    "metadata": {
        "kernelspec": {
            "display_name": "Python 3.5", 
            "name": "python3", 
            "language": "python"
        }, 
        "language_info": {
            "mimetype": "text/x-python", 
            "nbconvert_exporter": "python", 
            "version": "3.5.5", 
            "name": "python", 
            "file_extension": ".py", 
            "pygments_lexer": "ipython3", 
            "codemirror_mode": {
                "version": 3, 
                "name": "ipython"
            }
        }
    }, 
    "nbformat": 4
}
