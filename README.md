# Update plugin with corrected admin menu and settings page logic
plugin_dir = "/mnt/data/adsense-optimizer-fixed"
os.makedirs(plugin_dir, exist_ok=True)

plugin_main_php = """<?php
/**
 * Plugin Name: AdSense Auto Optimizer
 * Description: Automatically optimizes posts for Google AdSense on activation and provides a manual run button.
 * Version: 1.1
 * Author: ChatGPT Custom
 */

add_action('admin_menu', 'adsense_optimizer_menu');

function adsense_optimizer_menu() {
    add_menu_page(
        'AdSense Optimizer',
        'AdSense Optimizer',
        'manage_options',
        'adsense-optimizer',
        'adsense_optimizer_page',
        'dashicons-megaphone',
        100
    );
}

function adsense_optimizer_page() {
    if (isset($_POST['run_optimizer'])) {
        adsense_auto_optimizer_run();
        echo '<div class="notice notice-success is-dismissible"><p>All posts optimized successfully!</p></div>';
    }

    echo '<div class="wrap">';
    echo '<h1>AdSense Optimizer</h1>';
    echo '<form method="post">';
    echo '<p>This tool will insert your AdSense code automatically after the first paragraph in all posts.</p>';
    echo '<input type="submit" name="run_optimizer" class="button-primary" value="Run Optimization Now">';
    echo '</form>';
    echo '</div>';
}

function adsense_auto_optimizer_run() {
    $args = array(
        'post_type' => 'post',
        'posts_per_page' => -1
    );
    $posts = get_posts($args);
    foreach ($posts as $post) {
        $content = $post->post_content;

        // Avoid duplicate insertion
        if (strpos($content, '[Your AdSense Code Here]') !== false) {
            continue;
        }

        $adsense_code = '<div class="adsense-ad">[Your AdSense Code Here]</div>';
        $paragraphs = explode('</p>', $content);
        if (count($paragraphs) > 1) {
            $paragraphs[1] .= $adsense_code;
        }
        $new_content = implode('</p>', $paragraphs);

        wp_update_post(array(
            'ID' => $post->ID,
            'post_content' => $new_content
        ));
    }
}
?>
"""

# Save plugin PHP file
main_php_path = os.path.join(plugin_dir, "adsense-auto-optimizer.php")
with open(main_php_path, "w") as f:
    f.write(plugin_main_php)

# Create updated zip
zip_path_fixed = "/mnt/data/adsense-auto-optimizer-fixed.zip"
with ZipFile(zip_path_fixed, "w") as zipf:
    zipf.write(main_php_path, arcname="adsense-auto-optimizer.php")

zip_path_fixed
