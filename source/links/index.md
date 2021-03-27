---
title: 先驱者
date: 2021-02-12 23:32:57
type: "links"
---

{% block content %}
    {######################}
    {### LINKS BLOCK ###}
    {######################}

    <div id="links">
        <style>

            #links{
               margin-top: 5rem;
            }

            .links-content{
                margin-top:1rem;
            }

            .link-navigation::after {
                content: " ";
                display: block;
                clear: both;
            }

            .card {
                width: 300px;
                font-size: 1rem;
                padding: 10px 20px;
                border-radius: 4px;
                transition-duration: 0.15s;
                margin-bottom: 1rem;
                display:flex;
            }
            .card:nth-child(odd) {
                float: left;
            }
            .card:nth-child(even) {
                float: right;
            }
            .card:hover {
                transform: scale(1.1);
                box-shadow: 0 2px 6px 0 rgba(0, 0, 0, 0.12), 0 0 6px 0 rgba(0, 0, 0, 0.04);
            }
            .card a {
                border:none;
            }
            .card .ava {
                width: 3rem!important;
                height: 3rem!important;
                margin:0!important;
                margin-right: 1em!important;
                border-radius:4px;

            }
            .card .card-header {
                font-style: italic;
                overflow: hidden;
                width: 236px;
            }
            .card .card-header a {
                font-style: normal;
                color: #2bbc8a;
                font-weight: bold;
                text-decoration: none;
            }
            .card .card-header a:hover {
                color: #d480aa;
                text-decoration: none;
            }
            .card .card-header .info {
                font-style:normal;
                color:#a3a3a3;
                font-size:14px;
                min-width: 0;
                text-overflow: ellipsis;
                overflow: hidden;
                white-space: nowrap;
            }
        </style>

        <div class="links-content">
            <div class="no-icon note warning">
                <div class="link-info">👨‍🎓 休闲娱乐必备</div>
            </div>
            <div class="link-navigation">
                <div class="card">
                    <img class="ava nofancybox" src="../../images/4k.aa8e6f7.png" />
                    <div class="card-header">
                        <div>
                            <a href="https://dianying4k.com" target="_blank">artist</a>
                        </div>
                        <div class="info">人生得意需尽欢</div>
                    </div>
                </div>
            </div>
            <div class="link-navigation">
                <div class="card">
                    <img class="ava nofancybox" src="../../images/kanshuv.png" />
                    <div class="card-header">
                        <div>
                            <a href="https://www.kanshuv.com" target="_blank">writer</a>
                        </div>
                        <div class="info">读书破万卷，下笔如有神</div>
                    </div>
                </div>
            </div>

            <div class="no-icon note primary">
                <div class="link-info">🍭 人生不只变强</div>
            </div>
            <div class="link-navigation">
                <div class="card">
                    <img class="ava nofancybox" src="../../images/xublog.png"/>
                    <div class="card-header">
                    <div><a href="http://xublog.wang" target="_blank">architect</a> </div>
                    <div class="info">talk is cheap, show me the code</div>
                    </div>
                </div>
            </div>
            <div class="link-navigation">
                <div class="card">
                    <img class="ava nofancybox" src="../../images/computer_tech_book.jpg"/>
                    <div class="card-header">
                    <div><a href="https://github.com/itdevbooks/pdf" target="_blank">Feng Lei</a> </div>
                    <div class="info">书山有路于此，学海无涯向前</div>
                    </div>
                </div>
            </div>

            {{ page.content }}
            </div>
        </div>

  {##########################}
  {### END LINKS BLOCK ###}
  {##########################}
{% endblock %}
